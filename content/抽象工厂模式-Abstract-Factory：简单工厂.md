---
title: 抽象工厂模式(Abstract Factory)：简单工厂
date: 2016-01-15T17:28:48+08:00
tags: ["设计模式学习笔记"]
categories: ["设计模式"]
permalink: Abstract-Factory-simple-factory
description: 抽象工厂模式(Abstract Factory)：简单工厂
---
　　学习抽象工厂之前，我们有必要对另外两个：简单工厂、工厂方法进行学习，以更好的掌握抽象工厂的使用。

### 简单工厂
　　简单工厂并不是一种模式，而是面向对象方式的一种体现。<!--more-->
![](/image/Design Pattern/design pattern6.png)
　　**特点**：
　　1、只有一个工厂
　　2、只有一种类型的产品(和子类的数量无关)
　　3、一个工厂只生产一种产品

我们按照图中的例子编写代码，演示什么是简单工厂：
**Code 1**
　　面向过程的写法。
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace SimpleFactory1
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

            int numberResult = 0;  //运算结果
            switch (sign)
            {
                case "+":numberResult = number1 + number2;break;
                case "-": numberResult = number1 + number2; break;
                case "*": numberResult = number1 + number2; break;
                case "/": numberResult = number1 + number2; break;
                default:
                    numberResult = number1 + number2;
                    break;
            }

            Console.WriteLine("运算结果是：{0}",numberResult);
            Console.ReadKey();
        }
    }
}
```
运行结果：
![](/image/Design Pattern/design pattern7.png)

**Code 2**
　　稍微改写一下，将返回结果封装起来。
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace SimpleFactory2
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

            int numberResult = Result(number1, number2, sign);
            Console.WriteLine("运算结果是：{0}", numberResult);
            Console.ReadKey();
        }

        /// <summary>
        /// 返回计算结果的方法
        /// </summary>
        /// <param name="number1">第一个数字</param>
        /// <param name="number2">第二个数字</param>
        /// <param name="sign">运算符</param>
        /// <returns>返回运算结果</returns>
        private static int Result(int number1, int number2, string sign)
        {
            int numberResult = 0;
            switch (sign)
            {
                case "+": numberResult = number1 + number2; break;
                case "-": numberResult = number1 + number2; break;
                case "*": numberResult = number1 + number2; break;
                case "/": numberResult = number1 + number2; break;
                default:
                    numberResult = number1 + number2;
                    break;
            }
            return numberResult;
        }
    }
}
```
运行结果：
![](/image/Design Pattern/design pattern8.png)

**Code 3**
　　将运算抽象出来，使用运算符对象实例，进行计算。
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace SimpleFactory3
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
            switch (sign)
            {
                case "+":
                    Computer computerAdd = new Add();
                    computerAdd.Number1 = number1;
                    computerAdd.Number2 = number2;
                    numberResult = computerAdd.numberResult;
                    break;
                case "-":
                    Computer computerSubtraction = new Subtraction();
                    computerSubtraction.Number1 = number1;
                    computerSubtraction.Number2 = number2;
                    numberResult = computerSubtraction.numberResult;
                    break;
                case "*":
                    Computer computerMultiplication = new Multiplication();
                    computerMultiplication.Number1 = number1;
                    computerMultiplication.Number2 = number2;
                    numberResult = computerMultiplication.numberResult;
                    break;
                case "/":
                    Computer computerDivision = new Division();
                    computerDivision.Number1 = number1;
                    computerDivision.Number2 = number2;
                    numberResult = computerDivision.numberResult;
                    break;
                default:
                    numberResult = 0;
                    break;
            }
            Console.WriteLine("计算结果是：{0}",numberResult);
            Console.ReadLine();
        }
    }

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
![](/image/Design Pattern/design pattern9.png)

**Code 4**
　　我们这一步改写一下*Code 3*中的代码,简化`switch`中的代码。
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace SimpleFactory4
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
            Computer computer = null;
            switch (sign)
            {
                case "+": computer =new Add(); break;
                case "-": computer =new Subtraction(); break;
                case "*": computer =new Multiplication(); break;
                case "/": computer = new Division(); break;
                default:
                    numberResult = 0;
                    break;
            }
            number1 = computer.Number1;
            number2 = computer.Number2;
            numberResult = computer.numberResult;
            Console.WriteLine("计算结果是：{0}",numberResult);
            Console.ReadKey();
        }
    }

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
![](/image/Design Pattern/design pattern10.png)

**Code 5**
　　现在将`switch`封装成简单工厂方法。
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace SimpleFactory5
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
            Computer computer = null;
            computer = SimpleFactory.ResultComputer(sign);
            computer.Number1 = number1;
            computer.Number2 = number2;
            numberResult = computer.numberResult;

            Console.WriteLine("运算结果是：{0}",numberResult);
            Console.ReadKey();
        }

    }
    public class SimpleFactory
    {
        public static Computer ResultComputer(string sign)
        {
            Computer computer = null;
            switch (sign)
            {
                case "+": computer = new Add(); break;
                case "-": computer = new Subtraction(); break;
                case "*": computer = new Multiplication(); break;
                case "/": computer = new Division(); break;
                default:
                    computer = null;
                    break;
            }
            return computer;
        }
    }

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
![](/image/Design Pattern/design pattern11.png)
