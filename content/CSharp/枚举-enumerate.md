---
title: 枚举(enumerate)
date: 2015-06-04T23:14:38+08:00
tags: ["c#学习笔记"]
categories: ["cSharp"]
permalink: c#-enumerate
description: 枚举(enumerate)
---
## 什么是枚举
　　枚举是程序员自己定义的类型，与类和结构一样。
　　■ 与结构一样，枚举是值类型，因此直接存储它们的数据，而不是分成引用和数据去存储。
　　■ 不能对枚举成员使用修饰符，它们都隐式的具有和枚举相同的可访问性。
　　■ 枚举只有一种类型的成员：命名的整数值常量。
```
//声明枚举的关键字是“enum”
enum TrafficLight
{
    Green,  //成员之间用逗号分隔
    Yellow,  
    Red
}
```
<!--more-->
　　**每一个枚举类型都有一个底层整数类型，默认为int。**
　　■ 每一个枚举成员都被赋值一个默认的int类型的常量值。
　　■ 编译器把第一个成员默认的赋值为0，此后每一个成员都在前一个成员的基础上多1。
```
TrafficLight t1 = TrafficLight.Green;
TrafficLight t2 = TrafficLight.Yellow;
TrafficLight t3 = TrafficLight.Red;

//将枚举成员强制转换成int类型的底层整数值
Console.WriteLine("{0}---{1}",t1,(int)t1);
Console.WriteLine("{0}---{1}",t2,(int)t2);
Console.WriteLine("{0}---{1}",t3,(int)t3);
Console.ReadKey();
```
运行结果：
![](/image/cSharp/cSharp85.png)
枚举的成员常量被底层整数值表示：
![](/image/cSharp/cSharp86.png)
　　**可以把枚举值赋值给枚举类型的变量。**
```
TrafficLight t1 = TrafficLight.Green;
TrafficLight t2 = TrafficLight.Yellow;
//将枚举值赋值给枚举类型的变量
TrafficLight t3 = t2;
```

## 设置底层类型和显式值
　　**可以修改枚举类型的底层类型**，可以使用冒号和类型名使枚举的底层类型使用**char以外**的任何整数类型。
```
//将底层类型由默认的int修改成ulong
enum TrafficLight : ulong
{
    ...
}
```
　　**成员常量的值可以显式的设置**，可以在枚举声明的时候进行初始化成员常量的值，**枚举不能有重复的名称，但是可以有重复的成员常量值。**
```
enum TrafficLight
{
    Green = 10,
    Yellow = 15,
    Red = 15
}
```

## 隐式成员编号
   **可以显式的给任何成员常量赋值，如果没有赋值，编译器会隐式的给它赋一个值。**
```
//编译器默认赋值
enum CardSuit
{
    Hearts,       //0
    Clubs,        //1
    Diamonds,     //2
    Spades,       //3
    MaxSuits      //4
}

//显式的赋值
enum FaceCards
{
    Jack = 11,             //11
    Queen,                 //12 (在前一个的基础上多1)
    King,                  //13
    Ace,                   //14
    NumberOfFaceCards = 4, //4
    SomeOtherValue,        //5
    HighesFaceCard = Ace   //14 
}
```
成员赋值的法则：
![](/image/cSharp/cSharp87.png)

## 枚举成员可以直接使用
　　由于枚举成员是常量，所以可以不用创建枚举类型的变量，直接使用枚举成员。
```
enum MyEnum
{
    one,
    two,
    three
}
class Program
{
    static void Main(string[] args)
    {
        //没有创建枚举变量，可以直接使用枚举成员
        Console.WriteLine("{0}",MyEnum.one);
        Console.WriteLine("{0}",MyEnum.two);
        Console.WriteLine("{0}",MyEnum.three);
    }
}
```
运行结果：
![](/image/cSharp/cSharp88.png)

## 不同枚举类型的成员之间无法比较
```
enum FirstEnum
{
    Mem1,
    Mem2
}

enum SecondEnum
{
    Mem1,
    Mem2
}

class Program
{
    static void Main(string[] args)
    {
        if(FirstEnum.Mem1 < FirstEnum.Mem2)
        {
            //正确，相同的枚举类型进行比较
        }
        if(FirstEnum.Mem1 < SecondEnum.Mem1)
        {
            //错误，不同的枚举类型进行比较会发生编译期错误
        }
    }
}
```
