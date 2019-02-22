---
title: 接口(interface)
date: 2015-06-03T01:08:11+08:00
tags: ["c#学习笔记"]
categories: ["cSharp"]
permalink: c#-interface
description: 接口(interface)
---
# 1. 什么是接口
　　接口是一种能力的体现。
　　接口是表示一组**函数成员**而不是实现成员的引用类型，类和结构可以实现接口。

# 2. 声明接口
```csharp
//声明接口的关键字是“interface”
interface IMyInterface
{
    //接口中的方法没有方法体(也就是没有具体的实现)
    int GetSum(int num1,int num2); 
    string Speak(string str);
}
```
<!--more-->
　　■ 接口中不包含数据成员
　　■ 接口声明只能包含如下如下类型的静态成员函数的声明：
　　　　□ 方法
　　　　□ 属性
　　　　□ 事件
　　　　□ 索引
　　■ 这些函数成员的声明不能包含任何实现代码，在每一个成员声明的主体后面必须使用封号。
　　■ 一般情况下，建议接口的名称以大写字母“**I**”开头。

# 3. 访问性：
　　■ 接口的声明可以使用任何访问修饰符public、protected、internal、private。
　　■ 因为接口中的成员隐式的是public修饰的，所以接口中成员不允许有任何访问修饰符，包括public。

# 4. 实现接口
　　**只有类和结构才能实现接口。**要实现接口，类和结构必须：
　　■ 在基类列表(继承列表)中包括接口的名称。
　　■ 需要实现接口中的每一个成员。
```csharp
class MyClass:IMyInterface    //实现接口
{
    int GetSum(int num1,int num2)
    {
        return num1+num2;   //实现接口的成员
    }
    string Speak(string str)
    {
        return "你说接口是："+str;  //实现接口的成员
    }
}
```
　　★ 如果类实现了接口，它必须实现接口中的所有成员。
　　★ 如果类继承了基类并且又要实现接口，那么在基类列表中必须把基类的名称放在第一位，也就是必须放在接口的前面。

# 5. 接口是引用类型
　　接口不仅仅是类或结构需要实现的成员列表，它还是一个引用类型。
　　所以我们不能直接通过类对象的成员访问接口，但是可以通过把类对象引用强制转换为接口类型来获取指向接口的引用。一旦有了接口的引用，就可以使用点号调用接口的方法了。
```csharp
//“mc”是实现了接口IMyInterface的类对象引用，使用强制转换语法转换为接口类型
IMyInterface imi= (IMyInterface) mc;
//使用接口的引用调用方法
imi.Speak("引用类型");   
```
![](/image/cSharp/cSharp59.png)

# 6. 实现多个接口
　　★ 类或者结构可以实现任意数量的接口。
　　★ 所有实现的接口必须列在基类列表中，并且以逗号分隔。(如果同时继承了基类，则基类名称必须在最前面)
```csharp
interface ISum
{
    int GetSum();
}
interface ISpeak
{
    void Speak(string str);
}

//继承多个接口，必须实现每一个接口的全部成员
class MyClass:ISum,ISpeak
{
    int num;
    public int GetSum()
    {
        return num;
    }
    public string Speak(string str)
    {
        Console.WriteLine("接口是：{0}",str);
    }
}
```
![](/image/cSharp/cSharp60.png)

# 7. 实现具有重复成员的接口
　　类可以实现多个接口，如果有两个或以上的接口中的成员具有相同的签名和返回类型，这时候应该怎么让类去实现这些具有重复成员的接口？
　　● 这种情况下，类只需要实现单个成员，就能满足所有包含重复成员的接口。
```csharp
interface IIf1
{
    void PrintOut(string str);
}
interface IIF2
{
    void PrintOut(string str);
}

class MyClass:IIf1,IIf2
{
    void PrintOut(string str)
    {
        Console.WriteLine(str);
    }
}

class Program
{
    static void Main(string[] args)
    {
        MyClass mc = new MyClass();
        mc.PrintOut("只需实现单个成员！")
    }
}
```
![](/image/cSharp/cSharp61.png)

# 8. 派生成员作为实现
　　如果一个基类中的成员与接口的成员一致，那么派生自该基类的派生类就可以继承父类的成员而无需实现接口的成员。
```csharp
interface IIf
{
    void PrintOut(string str);
}

class MyClass 
{
    public void PrintOut(string str)
    {
        Console.WriteLine(str);
    }
}
class Derived : MyClass , IIf
{
    
}

class Program
{
    static void Main(string[] args)
    {
        Derived d = new Derived();
        d.PrintOut("interface");
    }
}
```
![](/image/cSharp/cSharp62.png)

# 9. 显式接口成员实现
　　我们可以为重复的成员实现单个的实现，如果需要为每一个接口分离实现，可以创建**显式接口成员实现**。
```csharp
using System;

namespace Interface
{
    class Program
    {
        static void Main(string[] args)
        {
            MyClass mc = new MyClass();
            IIf1 iif1 = mc;
            IIf2 iif2 = mc;
            iif1.PrintOut("IIf1接口的实现");
            iif2.PrintOut("IIf2接口的实现");
            Console.ReadKey();
        }
    }

    interface IIf1
    {
        void PrintOut(string str);
    }
    interface IIf2
    {
        void PrintOut(string str);
    }

    class MyClass : IIf1, IIf2
    {
        void IIf1.PrintOut(string str)
        {
            Console.WriteLine(str);
        }

        void IIf2.PrintOut(string str)
        {
            Console.WriteLine(str);
        }
    }
}
```
输出结果：
![](/image/cSharp/cSharp63.png)
　　★ **显式接口成员只能由指向接口的引用来调用**，如上面代码中的`iif1`和`iif2`。实现了显示接口成员的类对象是无法调用接口成员的：![](/image/cSharp/cSharp64.png)
　　★ 由于其他类不能直接访问显式接口成员实现，派生类的成员也不能直接访问它们，**它们总是必须通过接口的引用来访问**。

# 10. 接口可以继承接口
　　类可以实现接口，接口也可以继承接口。
　　■ 接口的继承和类继承类似，都是由冒号分隔的。
　　■ 与类不一样的是，接口可以继承多个接口，而类只能继承一个类。
　　■ 子接口包含了它自己的所有成员和所有基接口的所有成员。
