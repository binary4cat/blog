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

