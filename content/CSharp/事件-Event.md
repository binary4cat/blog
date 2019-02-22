---
title: 事件(Event)
date: 2015-05-21T23:07:00+08:00
tags: ["c#学习笔记"]
categories: ["cSharp"]
permalink: c#-Event
description: c#事件
---
# 1. 事件是什么
　　事件是对象与对象之间的通信工具。
　　事件的很多方面和委托相似，事件就好像是被简化后的针对特殊用途的委托。<!--more-->
![](/image/cSharp/cSharp53.png)
　　◆ **触发事件**：调用或者触发事件的术语。当事件被触发时，所有注册到它的方法都会被依次调用。
　　◆ **发布者**(publisher)：让事件被其他类或者结构可见并且使用的类或者结构。
　　◆ **订阅者**(subscriber)：把事件和发布者关联注册的类或者结构。
　　◆ **事件处理程序**(event handler)：注册到事件的方法。可以在事件所在的类或结构中，也可以在不同的类或者结构中。

## 1.1. 事件包含私有委托
　　事件和委托之所以相似，因为事件中包含了一个私有控制的委托：
　　■ 事件提供了对它自己包含的这个私有委托的结构化访问(高度控制)。
　　■ 事件只能添加、删除和调用事件处理程序(方法)，这点和委托不一样。
　　■ 事件被触发时，它调用委托来依次调用调用列表中的方法。
![](/image/cSharp/cSharp54.png)
　　**“+=”、“-=”是事件唯一允许的运算符。**

　　下面的图中演示了一个具有计时器事件类的运行结构：
　　● 发布者类中包含这一个名为`Elapsed`的事件。
　　● 右边的`ClassA`和`ClassB`是两个订阅者类，它们每一个都在`Elapsed`事件上注册了事件处理程序(也就是`TimerHandlerA()`和`TimerHandlerB()`)。
　　● 在`Elapsed`事件内，我们可以看到委托引用了两个事件处理程序。
　　● 在发布者类中，除了事件以外，还包含了触发事件的代码。
![](/image/cSharp/cSharp55.png)

## 1.2. 事件的代码组成结构
![](/image/cSharp/cSharp56.png)
　　■ 委托类型声明：事件和事件处理程序必须有共同的签名和返回类型，它们通过委托类型声明进行设定。
　　■ 事件处理程序声明：这些在订阅者类中的方法(事件处理程序)会在事件触发时被执行。它们不需要独立的的方法，可以是简单的匿名方法或者lambda表达式，只需要执行处理代码即可。
　　■ 事件声明：存在于发布者类中的声明，保存并调用事件处理程序。
　　■ 事件注册：把事件连接到事件处理程序。
　　■ 触发事件的代码：在发布者类中，触发事件的执行，进而导致事件调用事件处理程序。

## 1.3. 声明事件
　　**发布者类必须提供事件和触发事件的代码。**
　　创建事件比较简单，只需要委托类型和事件名字。
![](/image/cSharp/cSharp57.png)
　　■ Elasped事件被声明在MyTimerClass类(发布者类)中。
　　■ Elasped事件接受返回值类型、签名与EventHandler委托类型匹配的事件处理程序。
　　■ Elasped事件被public关键字修饰，所以其他的类或者结构都可以在这上面注册事件处理程序。

　　还可以使用逗号分隔声明多个事件：
　　`public event EventHandler MyEvent1,MyEvent2,MyEvent3;`

　　还可以使用static关键字修饰，让事件变成静态的。
　　`public static event EventHandler Elapsed;`


## 1.4. 事件是成员
　　事件不是类型，而是成员。
　　■ 由于事件不是类型，我们不能使用对象创建表达式(new)来创建它的对象。
　　■ 由于事件是成员：
　　　　□ 它必须声明在类或者结构中，与类或结构中的其他成员一样。
　　　　□ 不能再一段可执行代码中声明事件，因为事件是被动触发的。
　　■ 事件成员被隐式的自动初始化为null。

## 1.5. 委托类型和EventHandler
　　事件的声明需要委托类型的名字，我们可以声明一个委托类型或者使用BCL(基类库)提供的，如果我们声明一个委托类型，它必须指定注册到事件的事件处理程序的方法签名和返回类型，使用BCL指定为事件使用标准的预定义委托类型，推荐使用**EventHandler**。
　　`public delegate void EventHandler(object sender,EventArgs e);`

# 2. 触发事件
　　事件需要被动的触发才能够执行，事件成员本身只保存事件被触发时要调用的事件处理程序。
　　■ 事件在触发之前可以和`null`进行比较，从而查看该事件是否包含任何事件处理程序，如果事件为null，则表示该事件没有任何事件处理程序。
　　■ 触发事件看起来和调用函数一样：
　　　　□ 使用事件名称、后面跟的参数列表包含在圆括号中。
　　　　□ 参数列表必须和事件的委托类型相匹配。
```csharp
if(Elapsed!=null)          //确认该事件有可用的事件处理程序
{
    Elapsed(source,args);  //调用事件
}
```
　　**把事件声明和触发事件的代码放在一起，便组成了发布者类。**
```csharp
public class MyTimerClass
{
    public event EventHandler Elapsed;    //声明事件
    private void OnOneSecond(object source,EventArgs args)  //调用该方法就会触发事件的执行
    {
        if(Elapsed!=null)
        {
            Elapsed(source,args);  //发起事件
        }
    }
}
```
 　　■ 发布者类有一个做为成员的事件。
 　　■ 发布者类包含了触发事件的代码。
 
 ## 订阅事件
 　　为事件添加处理程序，**处理程序必须有和事件委托一致的返回类型和签名**。
 　　■ 使用**+=**为事件增加事件处理程序。
 　　■ 方法(事件处理程序)可以是下面的任意一个：
 　　　　□ 实例方法
 　　　　□ 静态方法
 　　　　□ 匿名方法
 　　　　□ lambda表达式
![](/image/cSharp/cSharp58.png)
　　和委托一样我们可以使用匿名方法或者lambda表达式尅增加事件处理程序。
```csharp
mc.Elapsed += (source,args) =>
    {
      Console.WriteLine("lambda事件处理程序！");  
    };
    
mc.Elapsed += delegate(source,asgs)
    {
        Console.WriteLine("匿名方法事件处理程序！");
    }
```

### 2.0.1. 代码示例：
```csharp
class Program
    {
        static void Main(string[] args)
        {
            ClassA ca = new ClassA();
            MyTimerClass mc = new MyTimerClass(); //创建发布者类

            //增加(订阅)事件处理程序
            mc.Elapsed += ca.TimerHandlerA; 
            mc.Elapsed += ClassB.TimerHandlerB;
        }
    }

    /// <summary>
    /// 发布者类
    /// </summary>
    public class MyTimerClass
    {
        public event EventHandler Elapsed;    //声明事件
        private void OnOneSecond(object source, EventArgs args)  //调用该方法就会触发事件的执行
        {
            if (Elapsed != null)
            {
                Elapsed(source, args);  //发起事件
            }
        }
    }

    /// <summary>
    /// 订阅者类
    /// </summary>
    public class ClassA
    {
        public void TimerHandlerA(object obj,EventArgs e)
        {
            Console.WriteLine("ClassA的事件处理程序");
        }
    }

    /// <summary>
    /// 订阅者类
    /// </summary>
    public class ClassB
    {
        public static void TimerHandlerB(object obj,EventArgs e)
        {
            Console.WriteLine("ClassB的事件处理程序");
        }
    }
```

## 2.1. 移除事件处理程序
　　使用**-=**运算符移除一个事件处理程序。
　　`mc.Elapsed -= ca.TimerHandlerA;   //移除事件处理程序A`

# 3. 标准事件的用法
　　**事件驱动的程序在程序运行时，它可以在任何时候被事件打断。**比如按钮点击事件、定时任务事件，在这些情况发生时，程序会在处理事件后继续其他的事情。
　　在c#中winfrom程序广泛的使用了事件，所以最好使用异步编程，.NET框架提供了一个标准的使用模式：
　　这个标准模式的根本就是`System`命名空间声明的`EventHandler`委托类型：
　　■ 第一个参数保存触发该事件的对象的引用，由于是object类型的，所以可以匹配任何类型的实例。
　　■ 第二个参数用来保存程序的有关状态信息，比如鼠标坐标等。
　　■ 返回值类型是void。

　　`EventHandler`委托的.Net声明代码：
```csharp
using System.Runtime.InteropServices;

namespace System
{
    //
    // 摘要:
    //     表示将处理不包含事件数据的事件的方法。
    //
    // 参数:
    //   sender:
    //     事件源。
    //
    //   e:
    //     不包含事件数据的对象。
    [ComVisible(true)]
    public delegate void EventHandler(object sender, EventArgs e);
}
```

# 4. 事件访问器
　　**+=、-=运算符时事件唯一允许的运算符**，这两个运算符有着预定义的行为(增加和删除事件处理程序的行为)。
　　我们可以编写代码自定义这两个运算符的行为，可以通过为事件定义事件访问器来实现。
　　■ 有两个访问器：add和remove。
　　■ add用来定义事件的“+=”运算符的执行代码，remove用来定义事件的“-=”运算符的执行代码。
　　■ 当事件声明了事件访问器之后，事件包含的私有委托就没有了，如果要增加和移除事件处理程序就需要自己实现。
　　■ 事件访问器表现为返回值为void的方法，所以不能使用return语句。
```csharp
public event EventHandler Elapsed
{
    add
    {
        //执行+=运算符时的代码
    }
    remove
    {
        //执行-=运算符时的代码
    }
}
```
