---
title: "初探System.Threading.Channels"
date: 2019-02-19T21:29:51+08:00
draft: false
tags: [".NET Core"]
permalink: 初探System.Threading.Channels
categories: ["cSharp"]
---

最近再次看了一下C#并发编程的一些资料，对其中的Rx产生了一些兴趣和疑问，同时想起对比一下Go语言的并发特性，所以查阅了一些资料，今天谈谈一个新的并发操作类库`System.Threading.Channels`。

# 1. 简介

`System.Threading.Channels`是.Net Core基础类库中实现的一个多线程相关的库，专门处理数据流相关的操作，用来在生产者和订阅者之间传递数据（不知道可不可以理解为线程间传递数据，我把它类比成了Go语言中的Channel），使用时需要通过NuGet安装。

这个库的前身是`System.Threading.Tasks.Channels`，来自实验性质的核心类库项目[https://github.com/dotnet/corefxlab](https://github.com/dotnet/corefxlab)，但是在2017年9月就不再更新了，目前使用的话需要用到最新的`System.Threading.Channels`库，如果你也是第一次接触的话，就直接上手研究`System.Threading.Channels`就可以了。

`Channel` API操作基于`Channel`对象，其操作主要由`ChannelReader`和`ChannelWriter`两部分组成，由`Channel`t提供的工厂方法创建一个有容量限制（或者无限制、最大容量限制）的`channel`。这点类似于Go语言中的`chan`的容量，二者在这里有很多的类似的地方，也有不同的地方。

## 1.1. 和Go语言channel的一些比较

Go语言中的channel默认是没有容量的，在使用这个没有容量的channel时，生产者和消费者必须“流动”起来，否则将会阻塞，也就是当生产者写入channel一个数据时，必须同时有一个接收者接收，否则写入操作会停止，等待有一个消费者取走channel中的数据，写入操作才会继续。

在`System.Threading.Channels`库中，没有类似Go语言的默认容量的机制，需要按需调用不同的`Channel`对象：

- `public static Channel<T> CreateBounded<T>(int capacity);` ：可以创建一个带有容量限制的`Channel`实例对象。
- `public static Channel<T> CreateBounded<T>(BoundedChannelOptions options)` ：创建一个自定义配置的`Channel`实例对象，可配置容量、以及在接收到新数据时的操作模式等等：
  - `BoundedChannelFullMode.Wait`：等待当前写入完成
  - `BoundedChannelFullMode.DropNewest`：删除并忽略管道中写入的最新的数据
  - `BoundedChannelFullMode.DropOldest`：删除并忽略管道中最旧的数据
  - `BoundedChannelFullMode.DropWrite`：删除当前正在写的数据，以写入管道中的新数据
- `public static Channel<T> CreateUnbounded<T>();` ：创建一个没有容量限制的`Channel`实例对象，在实际使用时应当谨慎使用该创建方式，因为可能会发生`OutOfMemoryException`。
- `public static Channel<T> CreateUnbounded<T>(UnboundedChannelOptions options)`：创建一个自定义配置的没有容量限制的`Channel`实例对象。该配置选项因为没有容量限制所以不会有写入等待操作模式，只有默认的一些配置：
  - `public bool SingleWriter { get; set; }`：是否需要一个一个读
  - `public bool SingleReader { get; set; }`：是否需要一个一个写
  - `public bool AllowSynchronousContinuations { get; set; }`：是否需要异步连续操作（我个人理解为异步操作时同时进行读写）

Go语言的channel机制和`System.Threading.Channels`的不同之处有两个：

1. Go语言没有无限容量的channel，而且就我个人的想法而言，无限容量并不“无限”，因为内存是有限的。
2. `System.Threading.Channels`没有单向的channel类型。在Go中可以创建“只读”或者“只写”的channel，但是`System.Threading.Channels`中没有提供这种操作。

## 1.2. 生产者、消费者需要的方法

生产者需要使用的一些方法：`TryWrite`/`WriteAsync`/`WaitToWriteAsync`/`Complete`
消费者需要使用的一些方法：`TryRead`/`ReadAsync`/`WaitToReadAsync`/`Completion`

方法介绍：

- `TryRead/TryWrite`：尝试使用同步方式读取或写入一项数据，返回读取或者写入是否成功。`TryRead`同时会以`out`的形式返回读取到的数据。
- `ReadAsync/WriteAsync`：使用异步方式写入或者读取一项数据。
- `TryComplete/Completion`：可以将channel标记为完成状态，这样就不会写入多余的错误数据，如果从已完成状态的channel中`ReadAsync`时会抛出异常，所以在不需要异步读取时建议经常使用`TryRead`。
- `WaitToReadAsync/WaitToWriteAsync`：在尝试读取或者写入数据之前，调用该方法可获得一个`Task<bool>`表示读取或者写入操作能否进行。

# 2. 示例代码

创建一个控制台程序演示channel的用法：

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Channels;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
            Task.Run(async () =>
            {
                await ChannelRun(0,0, 1, 50, 5);
            });
            Console.WriteLine("运行开始...");
            Console.ReadLine();
        }

        /// <summary>
        /// channel运行
        /// </summary>
        /// <param name="readDelayMs">读取器每次读取完等待时间</param>
        /// <param name="writeDelayMs">写入器每次写入完等待时间</param>
        /// <param name="finalNumberOfReaders">几个读取器同时读取</param>
        /// <param name="howManyMessages">写入器总共写入多少消息</param>
        /// <param name="maxCapacity">channel最大容量</param>
        /// <returns></returns>
        public static async Task ChannelRun(int readDelayMs, int writeDelayMs, int finalNumberOfReaders,int howManyMessages, int maxCapacity )
        {
            // 创建channel
            var channel = Channel.CreateBounded<string>(maxCapacity);
            var reader = channel.Reader;
            var writer = channel.Writer;


            var tasks = new List<Task>();
            // 读取器执行读取任务，可以设置多个读取器同时读取
            for (var i = 0; i < finalNumberOfReaders; i++)
            {
                var idx = i;
                tasks.Add(Task.Run(() => Read(reader, idx + 1,readDelayMs)));
            }

            // 写入器执行写入操作
            for (var i = 0; i < howManyMessages; i++)
            {
                Console.WriteLine($"写入器在{DateTime.Now.ToLongTimeString()}写入：{i}");
                await writer.WriteAsync($"发布消息： '{i}");
                // 写入完等待片刻
                await Task.Delay(writeDelayMs);
            }

            // 写入器标记完成状态
            writer.Complete();
            // 等待读取器读取完成
            await reader.Completion;
            // 等待读取器所有的Task完成
            await Task.WhenAll(tasks);

        }
        /// <summary>
        /// 读取数据任务
        /// </summary>
        /// <param name="theReader">读取器</param>
        /// <param name="readerNumber">读取器编号</param>
        /// <param name="delayMs">读取完等待时间</param>
        /// <returns>任务</returns>
        public static async Task Read(ChannelReader<string> theReader, int readerNumber, int delayMs)
        {
            // 循环判断读取器是否完成状态
            while (await theReader.WaitToReadAsync())
            {
                // 尝试读取数据
                while (theReader.TryRead(out var theMessage))
                {
                    Console.WriteLine($"线程{readerNumber}号读取器在{DateTime.Now.ToLongTimeString()}读取到了消息： '{theMessage}'");
                    // 读取完等待片刻
                    await Task.Delay(delayMs);
                }
            }
        }
    }
}
```

借助代码中的注释应当可以理解示例代码的作用，对其中的关键点做个说明：

- 写入器只有一个，写入的容量由channel的容量控制。
- 读取器可以设置多个，由`Task`调度同时读取。

## 2.1. 写入器、读取器无等待

写入器和读取器不等待，不停的读写数据，有一个读取器，总共写入50个数据，channel的容量为5，调用传参如下：

```csharp
Task.Run(async () =>
{
    await ChannelRun(0,0, 1, 50, 5);
});
```

结果：
![2019-02-20-1](/image/2019-02-20-1.gif)

写入读取操作在一秒内完成了，观察输出可以发现，写入和读取交替进行，写入的数据会立刻被读取器读取出来打印在终端内。

## 2.2. 读取器阻塞(等待)

将读取器的等待时间设置长一些，观察一下写入器是否会被阻塞，调用传参如下：

```csharp
Task.Run(async () =>
{
    await ChannelRun(10000,0, 1, 50, 5);
});
```

结果：
![2019-02-20-2](/image/2019-02-20-2.gif)

从输出的结果可以看见，在程序开始时写入器写入了6个数据(但是调试的时候capacity的值时5，这里的机制有待考证)，然后每过10秒读取器读取一个数据后，写入器才能写入一个数据，由于读取器的速度限制相当于将写入器也进行了阻塞。

## 2.3. 多个读取器同时读取

读取器还是每读取一次暂停10秒，但是有5个Task同时读取，调用传参如下：

```csharp
Task.Run(async () =>
{
    await ChannelRun(10000,0, 1, 50, 5);
});
```

结果：
![2019-02-20-3](/image/2019-02-20-3.gif)

从输出可以看出来，5个读取器Task可以每10秒钟同时读取5个数据，而写入器也同样的几乎是每次写入5个数据。

# 3. 总结

`System.Threading.Channels`作为一个线程间通信的库，用来当作发布者/订阅者组件使用非常方便。但是比起Go语言中的channel还是有些区别的，因为c#的`Async`/`Await`从某中意义上讲，并不是真正的多线程。

# 4. 附加内容（Go语言的channel使用方式）

```Golang
package main

import (
	"fmt"
	"time"
)

func main() {
	fmt.Println("运行开始...")
	channelRun(2000, 0, 5, 50, 10)
}

// channel运行实例
// readDelayMs, writeDelayMs分别是读取器需要暂停的时间和写入器需要暂停的时间
// finalNumberOfReaders是读取器个个数
// howManyMessages是消息的总数
// maxCapacity是channel的容量
func channelRun(readDelayMs, writeDelayMs, finalNumberOfReaders, howManyMessages, maxCapacity int) {
	// 创建channel
	channel := make(chan string, maxCapacity)
	for i := 0; i < finalNumberOfReaders; i++ {
		go func(i int) {
			read(channel, i, readDelayMs)
		}(i)
	}

	for i := 0; i < howManyMessages; i++ {
		fmt.Printf("写入器在%v写入：%v\n", time.Now().Format("2006-01-02 15:04:05.0000"), i)
		channel <- fmt.Sprintf("发布消息：%v", i)
		time.Sleep(time.Duration(writeDelayMs) * time.Millisecond)
	}
}

// 读取器从channel中读取数据
// channel时chan的实例
// readerNumner代表了当前Goroutine的编号
// delayMs表示当前的Goroutine读取完需要等待的时间
func read(channel chan string, readerNumber, delayMs int) {
	// 不断循环读取
	for {
		select {
		case msg := <-channel:
			fmt.Printf("%v号Gouroutine 读取器在%v读取到了消息：'%s'\n", readerNumber, time.Now().Format("2006-01-02 15:04:05.0000"), msg)
			time.Sleep(time.Duration(delayMs) * time.Millisecond)
		}
	}
}
```

结果：
![Snipaste_2019-02-20_23-04-24.png](/image/Snipaste_2019-02-20_23-04-24.png)

# 5. 参考资料

[https://medium.com/@alexyakunin/go-vs-c-part-1-goroutines-vs-async-await-ac909c651c11](https://medium.com/@alexyakunin/go-vs-c-part-1-goroutines-vs-async-await-ac909c651c11)
[https://github.com/dotnet/corefx/blob/master/src/System.Threading.Channels/tests/ChannelTests.cs](https://github.com/dotnet/corefx/blob/master/src/System.Threading.Channels/tests/ChannelTests.cs)
[https://sachabarbs.wordpress.com/2018/11/28/system-threading-channels/](https://sachabarbs.wordpress.com/2018/11/28/system-threading-channels/)