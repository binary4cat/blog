---
title: 抽象工厂模式(Abstract Factory)：工厂方法
date: 2016-01-23T00:10:49+08:00
tags: ["设计模式学习笔记"]
categories: ["设计模式"]
permalink: Abstract-Factory-the-factory-method
description: 抽象工厂模式(Abstract Factory)：工厂方法
---
　　学习抽象工厂之前，我们有必要对另外两个：简单工厂、工厂方法进行学习，以更好的掌握抽象工厂的使用。
>定义一个用于创建对象的接口，让子类决定实例化哪一个产品，Factory Method使得一个产品类的实例化延迟到子类。  ————GoF

　　 在软件系统中经常面临着某个对象(产品)的创建工作由于需求的变化，这个对象(产品)经常面临中剧烈的变化，但是他却拥有比较稳定的接口。<!--more-->

**类图**：
![](/image/Design Pattern/design pattern12.png)

　　可以看到这个类图在简单工厂的基础上多了一个*Creator*抽象类，用来“生产”工厂子类。
![](/image/Design Pattern/design pattern13.png)

**特点**
　　只有一种类型的产品
　　有N个工厂(这点与简单工厂不同的，简单工厂只有一个工厂，而工厂方法是把工厂又抽象出来了。)
　　一个工厂只生产一种产品

　　工厂方法的本质就是在简单工厂的基础上，又对工厂做了抽象。

**Code**
　　我们在简单工厂*Code5*代码基础上进行改写。
　　在下面的代码中，我们看似依旧需要`switch`进行子类工厂的创建，但是在具体的项目中，我们可以使用反射技术动态的加载创建的对象，这样体现出了工厂方法的便捷和解耦的好处。
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace FactoryMethod
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("请输入第一个数字:");
            int number1 = Convert.ToInt32(Console.ReadLine());
            Console.WriteLine("请输入第二个数字:");
            int number2 = Convert.ToInt32(Console.ReadLine());
            Console.WriteLine("请输入运算符:");
            string sign = Console.ReadLine();

            int numberResult = 0;
            //期望得到的产品
            Computer computer = null;
            //创建具体工厂
            Factory factory = null;
/************************工厂的创建和实例化，可以使用反射技术实现************************/
            switch (sign)
            {
                case "+": factory = new AddFactpry(); break;
                case "-": factory = new SubtractionFactory(); break;
                case "*": factory = new MultiplicationFactory(); break;
                case "/": factory = new DivisionFactory(); break;
                default:
                    factory = null;
                    break;
            }
/****************************************************************************************/

            //获得具体的产品。
            computer = factory.CreateComputer();  
            computer.Number1 = number1;
            computer.Number2 = number2;
            numberResult = computer.numberResult;

            Console.WriteLine("运算结果是：{0}", numberResult);
            Console.ReadKey();
        }
    }

    public abstract class Factory
    {
        //如果不是强制让工厂子类实现，可以使用virtual让子类有缺省实现。可以灵活运用。
        public abstract Computer CreateComputer();
    }
    public class AddFactpry : Factory
    {
        public override Computer CreateComputer()
        {
            //让工厂子类去创建具体的产品
            return new Add();
        }
    }
    public class SubtractionFactory : Factory
    {
        public override Computer CreateComputer()
        {
            return new Subtraction();
        }
    }
    public class MultiplicationFactory : Factory
    {
        public override Computer CreateComputer()
        {
            return new Multiplication();
        }
    }
    public class DivisionFactory : Factory
    {
        public override Computer CreateComputer()
        {
            return new Division();
        }
    }

    /**************************以下具体产品的生产不变*******************************/
    public abstract class Computer
    {
        public int Number1 { get; set; }
        public int Number2 { get; set; }
        //子类必须完成的功能：返回计算结果
        public abstract int numberResult { get; }
    }
    public class Add : Computer
    {
        public override int numberResult
        {
            get
            {
                return base.Number1 + base.Number2;
            }
        }
    }
    public class Subtraction : Computer
    {
        public override int numberResult
        {
            get
            {
                return base.Number1 - base.Number2;
            }
        }
    }
    public class Multiplication : Computer
    {
        public override int numberResult
        {
            get
            {
                return base.Number1 * base.Number2;
            }
        }
    }
    public class Division : Computer
    {
        public override int numberResult
        {
            get
            {
                return base.Number1 / base.Number2;
            }
        }
    }

}
```
运行结果：
![](/image/Design Pattern/design pattern14.png)

**代码注释**:
　　Factory Method模式可以有两种情况：一种是`Factory`类是一个抽象类，不需要提供子类的具体实现，只需让子类去实现它的抽象方法；另一种是`Factpry`是一个具体的类，它提供了子类的缺省实现，也就是使用`virtual`关键字修饰方法。
　　工厂方法中可以带带参数，这样也会比较方便客户使用，在初始化工厂方法的时候就可以传入生产产品所需要的参数。
　　工厂的作用并不仅仅是创建一个对象，它还可以做对象的初始化、参数的设置等等。

**适用性**：
　　当一个类不知道它所必须创建的对象的时候，或者希望由它的子类指定它所创建的对象的时候。
　　当一个类(工厂抽象类)将创建对象的工作交给了多个辅助子类(工厂子类)中的某一个，并且可以将需要由哪一个子类创建这一信息局部化(使用反射等技术方便的配置)的时候。
**总结**：
　　Factory Method模式通过面向对象的方式，将所要创建的具体对象的创建工作(new)延迟到了子类，从而提供了一种扩展的策略，较好的解决了这种紧耦合的关系。
　　Factory Method模式解决“单个对象”的需求变化，Abstract Factory模式解决“系列对象”的需求变化，Builder模式解决“对象部分”的需求变化。
