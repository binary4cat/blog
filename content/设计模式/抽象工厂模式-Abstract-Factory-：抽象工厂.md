---
title: 抽象工厂模式(Abstract Factory)：抽象工厂
date: 2016-01-23T17:12:30+08:00
tags: ["设计模式学习笔记"]
categories: ["设计模式"]
permalink: Abstract-Factory-abstract-factory
description: 抽象工厂模式(Abstract Factory)：抽象工厂
---
>提供一个接口，让该接口负责创建一系列“相关或相互依赖的对象”，无需指定他们的具体类。      ————GoF

　　在抽象工厂中有两种抽象存在：系列的抽象、产品的抽象。<!--more-->

![](/image/Design Pattern/design pattern15.png)

　　在这个类图中，*ConcreteFactory1*和*ConcreteFactory2*是系列的抽象，*AbstractProductA*和*AbstractProductB*是产品的抽象。
　　当客户端创建了*AbstractFactory*这个抽象工厂时，调用*ConcreteFactory1*和*ConcreteFactory2*创建出不同的产品A1、B1和A2、B2。

![](/image/Design Pattern/design pattern16.png)

**实现要点**：
　　抽象工厂将产品对象的创建延迟到它的具体工厂的子类。
　　通常在运行时创建一个具体工厂类的实例，这一具体工厂的创建具有特定实现的产品对象，为了创建不同的产品对象，客户应使用不同的工厂。
　　把工厂作为单件，一个应用中一般每个产品系列只需要一个具体的工厂实例，因此，工厂通常最好实现为一个单例模式。
　　创建产品，抽象工厂声明一个创建产品的接口，真正创建产品是由具体工厂类创建的，最通常的办法是为每一个产品定义一个工厂方法，一个具体的工厂将为每个产品重定义该工厂方法，以指定产品，虽然这样的实现很简单，但是它要求每个产品系列都要有一个新的具体工厂子类，即使这些产品系列的差别很小。

**Code**：
　　我们假设有两个系列(品牌)和两种产品，客户端只需要调用产品的抽象，而无须知道具体产品的工厂。
```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace AbstractFactory
{
    class Program
    {
        static void Main(string[] args)
        {
            //客户端只需要知道产品的抽象和工厂的抽象
            PC pc = null;
            Pad pad = null;
            Producer producer = null;

            //我们需要哪个系列的产品，就调用该工厂的具体实现(子工厂)
            //我们这里假设需要联想品牌的产品
            producer = new LenovoFactory();
            pc = producer.productionPC();
            pad = producer.productionPad();

            Console.WriteLine("电脑是：{0},平板是：{1}",pc,pad);
            Console.ReadKey();
        }
    }

    /// <summary>
    /// 产品A抽象
    /// </summary>
    public abstract class PC
    {

    }
    /// <summary>
    /// 产品B抽象
    /// </summary>
    public abstract class Pad
    {

    }

    /// <summary>
    /// 工厂的抽象
    /// </summary>
    public abstract class Producer
    {
        //制造PC
        public abstract PC productionPC();
        //制造平板
        public abstract Pad productionPad();
    }

    /// <summary>
    /// 系列1的产品A
    /// </summary>
    public class LenovoPC : PC
    {
        public override string ToString()
        {
            return "联想电脑";
        }
    }
    /// <summary>
    /// 系列2的产品A
    /// </summary>
    public class DellPC:PC
    {
        public override string ToString()
        {
            return "戴尔电脑";
        }
    }

    /// <summary>
    /// 系列1的产品B
    /// </summary>
    public class LenovoPad : Pad
    {
        public override string ToString()
        {
            return "联想平板电脑";
        }
    }
    /// <summary>
    /// 系列2的产品B
    /// </summary>
    public class DellPad:Pad
    {
        public override string ToString()
        {
            return "戴尔平板电脑";
        }
    }


    /// <summary>
    /// 联想工厂
    /// </summary>
    public class LenovoFactory : Producer
    {
        public override Pad productionPad()
        {
            return new LenovoPad();
        }

        public override PC productionPC()
        {
            return new LenovoPC();
        }
    }
    /// <summary>
    /// 戴尔工厂
    /// </summary>
    public class DellFactory : Producer
    {
        public override Pad productionPad()
        {
            return new DellPad();
        }

        public override PC productionPC()
        {
            return new DellPC();
        }
    }
}
```
运行结果：
![](/image/Design Pattern/design pattern17.png)

　　在上述代码中，我们的变化点在于创建(new)具体的工厂这一步骤，需要不同的系列的产品，就可以创建不同的子工厂，我们修改一下客户端调用的代码，就可以看出来了：
```csharp
static void Main(string[] args)
        {
            //客户端只需要知道产品的抽象和工厂的抽象
            PC pc = null;
            Pad pad = null;
            Producer producer = null;

            //我们需要哪个系列的产品，就调用该工厂的具体实现(子工厂)
            //我们这里假设需要联想品牌的产品
            //producer = new LenovoFactory();
            //*********戴尔系列产品*************
            producer = new DellFactory();
            //**********************************
            pc = producer.productionPC();
            pad = producer.productionPad();

            Console.WriteLine("电脑是：{0},平板是：{1}",pc,pad);
            Console.ReadKey();
        }
```
![](/image/Design Pattern/design pattern18.png)

　　同样的，我们可以使用反射来动态的实现工厂的创建(new)工作，这样就可以体现设计模式的好处了。


**适用性**：
　　一个系统不应当依赖于产品类实例如何被创建、组合和表达的细节，这对于所有形态的工厂模式都是重要的。
　　这个系统有多于一个产品系列，而系统只需要消费其中的某一个产品系列。
　　系统提供一个产品类的库，所有的产品以同样的接口出现，从而使客户端不依赖于实现。

**总结**：
　　如果没有应对“构建多个产品系列”的需求变化，则没有必要使用抽象工厂模式，这时候使用简单的静态工厂完全可以。
　　“系列对象”指的是这些对象之间有相互依赖或相互作用的关系。
　　抽象工厂模式主要是应对“新系列”的需求变动，但是缺点是难以应对“新产品”的需求变动。比如说上面的代码例子，我们如果需要添加华硕品牌，只需要继承*Producer*工厂的抽象就可以了，但是如果添加*Phone*这个新产品的话，就需要改动好几处工厂代码。
　　抽象工厂模式经常和工厂方法模式共同组合来应对“新产品”的需求变化。
