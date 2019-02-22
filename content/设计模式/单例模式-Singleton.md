---
title: 单例模式(Singleton)
date: 2016-01-11T11:26:52+08:00
tags: ["设计模式学习笔记"]
categories: ["设计模式"]
permalink: Design-patterns-Singleton
description: 单例模式(Singleton)
---
>保证一个类仅有一个实例，并且提供一个该实例的全局访问点。     ————GoF　　

　　
　　在软件系统中，经常有这样一些特殊的类，必须保证它们在系统中只存在一个实例，才能确保它们的逻辑正确性、以及良好的效率。
　　用户不能随便的使用*new*去创建一个类，这应该是设计者应该控制的。
![](/image/Design Pattern/design pattern1.png)<!--more-->

**简单实现**：
　　1、类内部维护一个静态的变量。
　　2、将构造函数私有化，让外界不能*new*。
　　3、创建一个全局的访问点。
　　**问题**：对于线程不安全。如果有两个线程同时首次访问这个单例，会同时判断这个访问点的变量，这时该变量为null，这样就会创建两个实例，不符合单例的要求。
```csharp
public sealed class Singleton
{
    static Singleton instance = null;  //类内部维护一个静态的变量
    private Singleton()   //将构造函数私有化，让外界不能*new*
    {
    }
    public static Singleton Instance   //创建一个全局的访问点
    {
        get
        {
            if (instance == null)
            {
                instance = new Singleton();
            }
            return instance;
        }
    }
}
```

**线程安全的实现**：
　　同一时刻加了锁的部分程序只有一个线程可以进入。
　　对象的实例由最先进入的那个线程创建。
　　后来的线程在进入时(instance==null)为假，不会再去创建对象的实例。
　　增加了额外的开销，损失了性能。
```csharp
public sealed class Singleton
{
    private static Singleton instance = null;
    private static readonly object padlock = new object();
    private Singleton()
    {
    }
    public static Singleton Instance
    {
        get
        {
            //加锁，当有几个线程同时访问时，使用内部机制安排一个继续执行，其余的等待。
            lock (padlock)
            {
                if (instance == null)
                {
                    instance = new Singleton();
                }
            }

            return instance;
        }
    }
}
```

**线程安全的实现(增强版)**：
　　增加一个额外的判断，就能避免额外的判断步骤，减少开销。
```csharp
public sealed class Singleton
{
    private static Singleton instance = null;
    private static readonly object padlock = new object();
    private Singleton()
    {
    }
    public static Singleton Instance
    {
        get
        {
            //增加一个额外的判断，就能阻止每次都进行加锁的额外步骤
            if (instance == null)
            {
                lock (padlock)
                {
                    if (instance == null)
                    {
                        instance = new Singleton();
                    }
                }
            }
            return instance;
        }
    }
}
```

**静态初始化**：
　　静态构造方法在程序运行的整个过程中只会执行一次，这是.net的内部机制。
　　静态构造方法会在该类的任何操作发生时触发，包括执行该类中的其他方法、调用其它属性，都会执行静态构造函数。
　　所以就有一个问题，如果我们需要在创建单例对象之前，执行该类中的其他方法，这时就会自动的执行静态构造方法，而此时我们并不需要这个单例对象，所以这不是一个完美的解决方法。
```csharp
public sealed class Singleton
{
    private static readonly Singleton instance = null;
    //在.net中静态构造方法的特性，来创建单例实例
    static Singleton()
    {
        instance = new Singleton(); 
    }
    //私有化构造函数，让外界不能new
    private Singleton() 
    {

    }
    public static Singleton Instance
    {
        get
        {
            return instance;
        }
    }
}
```

**延迟初始化**：
　　初始化的工作交给了*BuildSingleton*这个类的静态构造函数，这样就不会像“静态初始化”那样只要操作Singleton类，就会执行静态构造函数，这样就实现了延迟初始化。
```csharp
public sealed class Singleton
{
    private Singleton()
    {

    }
    public static Singleton Instance
    {
        get
        {
            return BuildSingleton.instance;
        }
    }
    //将静态构造函数放在类中，用到的时候触发自动执行单例对象的初始化。
    private class BuildSingleton
    {
        internal static readonly Singleton instance =null;
        static BuildSingleton()
        {
            instance = new Singleton();
        }
    }
}
```

**总结**：
　　Singleton模式中的实例构造函数可以设置为protected以允许子类派生。
　　Singleton模式一般不要支持ICloneable接口(克隆性质的接口)，因为这可能导致多个对象实例，与Singleton模式的初衷违背。
　　Singleton模式一般不要支持序列化，因为这也有可能导致多个对象实例。序列化对象可以把对象以不同的形式存储在内存中，如果将单例对象序列化，那么可能在内存中出现多个该对象。也不符合Singleton模式的要求。
　　Singleton模式只考虑到对象的创建(new的操作)，没有考虑到对象的销毁管理，在.net平台上使用GC垃圾回收，一般不需要对对象的销毁进行特殊的管理。
　　
　　Singleton模式是对类的限制，而不是对类进行改进扩展。
　　理解和扩展Singleton模式的核心是“如何控制用户使用new对一个类的构造函数进行任意的调用”。
　　可以对Singleton模式进行修改延伸，可以使它有少量的几个实例，这都是允许的。
