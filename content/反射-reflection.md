---
title: 反射(reflection)
date: 2015-06-18T16:01:14+08:00
tags: c#学习笔记
categories: c#
permalink: c#-reflection
description: 反射(reflection)
---
## 什么是反射
　　一个运行的程序查看本身或者其他程序的元数据的行为称为反射(reflection)。
　　元数据(metadata)：有关程序及其类型的数据称为元数据，它们保存在程序的程序集中。

　　**反射功能引用自system.Reflection命名空间**，要使用反射需要使用`System.Reflection`命名空间。
　　BCL(基类库)声明了一个叫做`Type`的抽象类，它被设计用来包含类型的特性，使用这个类的对象能让我们获取程序使用的类型的信息。
<!--more-->
### Type类
　　● 对于程序中用到的每一个类型，CLR都会创建一个包含这个类型信息的Type类型的对象。
　　● 程序中用到的每一个类型都会关联到独立的Type类对象。
　　● 不管创建的类型有多少个实例，只有一个Type对象会关联到这些实例。

　　我们可以从Type对象中获取需要了解的有关类型的几乎所有的信息，Type类的几个主要成员：
![](http://ww3.sinaimg.cn/mw690/c55a7aeejw1f1f9uf40o5j20qg07imxf.jpg)
 
### 获取Type对象
　　**Type是一个抽象类，所以不能直接使用new关键字创建Type的实例**。
　　有三种方法可以获取Type对象：`GetType()`、`Type.GetType()`、`typeof()`

　　**①：**`System.Object.GetType`方法是`object`类型所包含的一个方法，因为每一个类型都是继承自`object`的所以，我们可以在任何对象上使用`GetType`方法获得该类型的Type对象：`Type t = MyObject.GetType();`
```
static void Main(string[] args)
{
    Type t = MyObject.GetType();
    Console.WriteLine("{0}",t.Name);
    Console.WriteLine("{0}",t.Namespace);
    FieldInfo[] fi = t.GetFields();
    foreach(var f in fi)
    {
        Console.WriteLine("{0}",f.Name);
    }
}
```
　　**②：**使用`typeof`运算符来获取Type对象，只需要提供类型名作为参数它就会返回Type对象的引用:`Type t = typeof(MyObject);`
```
static void Main(string[] args)
{
    Type to = typeof(MyObject);
    Console.WriteLine("{0}",to.Name);
    Console.WriteLine("{0}",to.Namespace);
    FieldInfo[] fi = to.GetFields();
    foreach(var f in fi)
    {
        Console.WriteLine("{0}",f.Name);
    }
}
```
　　**③：**`System.Type.GetType()`是一个静态方法，有三个参数的重载：
　　1. 类型的完全限定名(命名空间.类名)，字符串类型。
　　2. 当前类型找不到时是否抛出异常，bool类型。
　　3. 是否区分字符串的大小写，bool类型。
```
//MyObject类的命名空间是“Reflection”，所以完全限定名就是：Reflection.MyObject
Type t = Type.GetType("Reflection.MyObject");

Type t1 = Type.GetType("Reflection.MyObject",true,true);
```

### 利用反射调用已存在类库的方法
基本步骤：
　　1. 加载程序集
　　2. 获得类型
　　3. 创建类型实例
　　4. 获得方法
　　5. 调用方法

　　首先我们创建一个类库，添加如下代码，然后生成dll文件：
```
   public class Conveyance
    {
        public virtual string reuturn()
        {
            return "这是交通工具";
        }
    }
    public class Car : Conveyance
    {
        public override string reuturn()
        {
            return "这是小汽车";
        }
    }
    public class Ship : Conveyance
    {
        public override string reuturn()
        {
            return "这是轮船";
        }
    }
    public class Plan : Conveyance
    {
        public override string reuturn()
        {
            return "这是飞机";
        }
    }
```
　　然后在程序中调用这个dll文件中的类：
```
        static void Main(string[] args)
        {
            //加载程序集(dll文件)
            Assembly a = Assembly.LoadFile(@"E:\cSharp练习\Conveyance\bin\Debug\Conveyance.dll");
            //获得类型
            Type[] t = a.GetTypes();
            foreach (Type item in t)
            {
                string str = item.ToString();
                //搜寻指定类型，使用系统激活器创建该类型的实例
                object objClass = a.CreateInstance(str);
                MethodInfo mi = item.GetMethod("reuturn");
                Console.WriteLine(mi.Invoke(objClass,null));
            }
            Console.ReadKey();
        }
```
运行结果：![](http://ww3.sinaimg.cn/mw690/c55a7aeejw1f1fdzu9662j20ry0gpjrc.jpg)