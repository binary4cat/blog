---
title: 结构(struct)
date: 2015-05-24T23:26:37+08:00
tags: ["c#学习笔记"]
categories: ["c#"]
permalink: c#-struct
description: 结构(struct)
---
## 什么是结构
　　结构是程序员定义的数据类型，非常类似于类，都有数据成员和函数成员。
　　结构就是轻量级的类，使用方式和枚举差不多，方便归类存储数据。
　　结构和类的区别是：
　　■ 类是引用类型而结构是值类型。
　　■ 结构是隐式密封的，这意味着结构不能被派生。
**声明结构**：
```
//声明结构的关键字是“struct”
struct StructName
{
    MemberDeclarations
}
```
<!--more-->
**使用结构**：
```
   struct Point
    {
        public int X;
        public int Y;
    }

    class Program
    {
        static void Main(string[] args)
        {
            Point first, second, third;

            first.X = 10;
            first.Y = 10;
            second.X = 20;
            second.Y = 20;
            third.X = first.X + second.X;
            third.Y = first.Y + second.Y;

            Console.WriteLine("first：{0}——{1}", first.X, first.Y);
            Console.WriteLine("second：{0}——{1}", second.X, second.Y);
            Console.WriteLine("third：{0}——{1}", third.X, third.Y);

            Console.ReadKey();
        }
    }
```
运行结果：
![](/image/cSharp/cSharp75.png)

## 结构是值类型
　　和所有的值类型一样，结构类型变量含有它自己的数据(而非引用)，从而：
　　■ 结构类型的变量不能为null。
　　■ 两个结构变量不能引用(保存、赋值)同一个对象。

```
//类
class CSimple 
{
    public int X;
    public int Y;
}
//结构
struct Simple
{
    public int X;
    public int Y;
}
class Program
{
    static void Main(string[] args)
    {
        CSimple cs = new CSimple();
        Simple ss = new Simple();
    }
}
```
类和结构在内存中的存储方式：
![](/image/cSharp/cSharp76.png)

## 对结构赋值
　　把一个结构赋值给另一个结构，就是从一个结构中把值**复制**到另一个结构。而复制类变量时只有引用被复制了，堆中的数据不变。
```
class CSimple
{
    public int X;
    public int Y;
}
struct Simple
{
    public int X;
    public int Y;
}
class Program
{
    static void Main(string[] args)
    {
        // 类实例
        CSimple cs1 = new CSimple();
        CSimple cs2 = null;
        // 结构实例
        Simple ss1 = new Simple();
        Simple ss2 = new Simple();
        
        cs1.X=5;
        ss1.X=5;
        cs1.Y=10;
        ss1.Y=10;
        cs2=cs1;   //赋值类实例
        ss2=ss1;   //复制结构实例
    }
}
```
在内存中存储的情况：
![](/image/cSharp/cSharp77.png)
　　■ 类赋值后，`cs2`和`cs1`指向堆中的同一对象。
　　■ 结构赋值后，`ss2`中的成员的值和`ss1`的相同。　

## 构造函数
　　**结构可以有实例构造函数和静态构造函数，但是不能有析构函数。**

### 实例构造函数
　　结构隐式的含有一个无参数的构造函数，这个无参数的构造函数默认的为结构中的每一个成员赋值了默认值(值类型被默认设置成它们的默认值，引用类型默认设置成null)。
　　隐式的无参构造函数在每个结构中存在，这个无参构造函数不能被删除，也不能重新定义。
　　**结构可以创建另外的含有参数的构造函数**。

　　调用构造函数有两种方法，一种是使用`new`关键字，一种是不使用`new`关键字。
**使用new关键字：**
```
struct Simple
{
    public int X;
    public int Y;
    // 带参数的构造函数
    public Simple(int a,int b)
    {
        X=a;
        Y=b;
    }
}

class Program
{
    static void Main()
    {
        //调用隐式的无参构造函数
        Simple s1 = new Simple();
        //调用定义的有参构造函数
        Simple s2 = new Simple(5,10);
        
        Console.WriteLine("{0},{1}",s1.X,s1.Y);
        Console.WriteLine("{0},{1}",s2.X,s2.Y);
    }
}
```
**不使用new关键字：**
　　■ 必须给数据成员赋值后才能使用该数据成员。
　　■ 必须给所有的数据成员都赋值之后，才能调用函数成员。
```
struct Simple
{
    public int X;
    public int Y;
}

class Program
{
    static void Main(string[] args)
    {
        // 不使用new关键字声明
        Simple s1,s2;
        
        //Console.WriteLine("{0},{1}",s1.X,s1.Y);  //因为没有赋值就使用，编译错误
        
        s2.X=5;
        s2.Y=10;
        Console.WriteLine("{0},{1}",s2.X,s2.Y);  //没有使用new创建实例，但是成员赋值之后是可以使用的。
    }
}
```

### 静态构造函数
　　结构的静态构造函数创建并初始化静态数据成员，而且不能引用实例成员。
　　结构的静态构造函数在调用显示声明的构造函数之前被调用。

## 字段不允许被初始化
　　**在结构中字段初始化是不允许的。**
```
struct Simple
{
    //这种写法是错误的，结构中的字段不允许初始化
    public int X = 5;
    public int Y = 10;
}
```

## 结构是密封的
　　结构总是密封的，所以，不能从结构派生其他的结构。
　　由于结构不支持继承，所以下列类修饰符不能用于结构：
　　● protected
　　● internal
　　● abstract
　　● virtual
　　**结构派生自System.ValueType类**

## 结构作为返回类型和参数
　　结构可以用作返回值和参数。
　　■ **返回值**：当结构被当做返回值时，一个该结构的拷贝被创建并从函数成员返回。
　　■ **值参**：当结构被当做值参时，一个实际参数的拷贝被创建，该拷贝用在方法的执行中。
　　■ **ref和out参数**：如果把一个结构用作ref或者out参数，一个对该结构的引用被传入方法，这样其数据成员就能被改变。

## 关于结构
　　分配结构比创建类的实例需要更少的消耗，所以使用结构代替类有时可以提高性能，但是结构是值类型，如果想使用一个结构实例作为引用类型的对象，必须执行拆装箱操作，代价比较高。
　　■ 预定义简单类型(int、short、long等等),尽管在.NET和c#中被视为原始类型，但是它们在.NET中都被实现为结构。
　　■ 可以使用声明partial类相同的方法声明partial结构。
　　■ 结构可以实现接口。
