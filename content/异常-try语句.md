---
title: 异常(try语句)
date: 2015-05-19T17:29:08+08:00
tags: c#学习笔记
categories: c#
permalink: Exceptions-try-statements
description: 异常(try语句)
---
### 什么是异常
　　**异常**是程序中的运行时错误，它违反了一个系统的约束或应用程序约束，或出现了在正常操作是位于聊的情形。

### try语句
　　**try**语句用来包裹住可能发生异常的代码块，如果异常发生的话，就提供代码处理该异常。<!--more-->
![](/image/cSharp/cSharp15.png)

### 异常类
　　有许多不同类型的异常可以在程序中发生。BCL定义了许多类，每一个类代表一个指定的异常类型，当一个异常发生时，CLR将：
　　■ 创建该类型的异常对象
　　■ 寻找适当的catch子句来处理它

　　所有的异常类从根本上都是派生自**System.Exception**类：
![](/image/cSharp/cSharp16.png)
　　异常对象含有只读属性，带有导致该异常的信息：
![](/image/cSharp/cSharp17.png)

### catch子句
　　**catch**子句处理异常，它有三种形式，允许不同级别的处理。
```
//一般catch子句
//在catch关键字后面没有参数列表
//匹配try块中引起的任何类型的异常
catch
{
    Statements
}

//特定catch子句
//带有异常类的名称作为单一参数
//匹配任何该名称类型的异常
catch(ExceptionType)
{
    Statements
}

//带对象的特定catch子句
//在异常类名称之后包括一个标识符
//该标识符在catch子句块中相当于一个本地变量，并被称为异常变量
//异常变量引用异常对象，并能被用于访问关于该变量的信息
catch(ExceptionType InstID)
{
    Statements
}
```
　　**一般catch子句**能够接受任何异常，但不能确定引发异常的类型。这只允许对任何可能发生的异常的普通处理和清理。
　　**特定catch子句**形式把一个异常类的名称作为参数。它匹配该指定类或派生自它的异常类的异常。
　　**带对象的特定catch子句**提供最多的关于异常的信息。它匹配该指定类的异常，或派生自它的异常类的异常。它还给出一个异常实例，称为**异常变量**，它是一个队CLR创建的异常对象的引用，可以在catch子句块内部访问异常变量的属性，以获取关于引起的异常的详细信息。

　　下面的代码处理IndexOutRangeException类型的异常，每当一个异常发生时，一个实际异常对象的引用被参数e传入代码。
```
//其中“IndexOutOfRangeException”是异常类型；“e”是异常变量；“e.Message”是访问异常变量
catch(IndexOutOfRangeException e)
{
    Console.WriteLine("Message:{0}",e.Message);
    Console.WriteLine("Source:{0}",e.Source);
    Console.WriteLine("Stack:{0}",e.StackTrace);
}
```

### 使用catch子句的示例
**一般catch子句**：
```
    class Program
    {
        static void Main(string[] args)
        {
            try
            {
                int a = 10;
                int b = 0;
                int c = a / b;
            }
            catch
            {
                throw;
            }
            Console.ReadKey();
        }
    }
```
一般catch子句会处理try块中引起的任何异常。
![](/image/cSharp/cSharp18.png)

**特定catch子句**：
```
    class Program
    {
        static void Main(string[] args)
        {
            try
            {
                int a = 10;
                int b = 0;
                int c = a / b;
            }
            catch(DivideByZeroException)
            {
                throw;
            }
            Console.ReadKey();
        }
    }
```
特定catch子句只会处理指定类型的异常信息。
![](/image/cSharp/cSharp19.png)

**带对象的特定catch子句**
```
    class Program
    {
        static void Main(string[] args)
        {
            try
            {
                int a = 10;
                int b = 0;
                int c = a / b;
            }
            catch(DivideByZeroException e)
            {
                Console.WriteLine("异常信息:{0}", e.Message);
                Console.WriteLine("导致错误的应用程序或对象名称:{0}", e.Source);
                Console.WriteLine("异常发生在:{0}", e.StackTrace);
            }
            Console.ReadKey();
        }
    }
```
访问异常对象的属性
![](/image/cSharp/cSharp20.png)

### catch子句段
　　catch子句段可以包含多个catch子句。
![](/image/cSharp/cSharp21.png)
　　当异常发生时，系统按照顺序搜索catch子句的列表，第一个匹配该异常对象类型的catch子句被执行。因此，catch子句的排序有两个重要的规则：
　　■ 特定catch子句必须以一种顺序排列，最明确的异常类型第一，知道最普通的类型。例如，如果声明了一个派生自NullRefrenceException的异常类，那么为派生该异常类型的catch子句应该被列在NullRefrenceException的catch子句之前。
　　■ **如果有一个一般catch子句，它必须是最后一个，并且在所有特定catch子句之后**。使用一般catch子句是不好的，应该尽可能使用特定catch子句，一般catch子句通过让程序继续执行隐藏错误，让程序处于一种未知的状态。

### finally块
　　如果程序的控制流进入了一个带有finally块的try语句，那么finally块始终会被执行。
![](/image/cSharp/cSharp22.png)
　　■　即使try块中有return语句，finally块也总是会在返回到调用代码之前执行。
```
    class Program
    {
        static void Main(string[] args)
        {
            int num = 5;
            try
            {
                if (num<10)
                {
                    Console.WriteLine("带有return语句的try块");
                    return;
                }
                else
                {
                    Console.WriteLine("不带return语句的try块");
                }
            }
            finally
            {
                Console.WriteLine("这是finally块中的输出！！");
                Console.ReadKey();
            }
            Console.ReadKey();
        }
    }
```
　　我们在这段代码中，我们首先让`num`的值小于10，这样就会执行`if`中的代码，我们就能看到带有`return`语句的try..finally语句是什么执行的。
![](/image/cSharp/cSharp23.png)
　　从结果中可以看到，先执行了`if`中的`Console.WriteLine()`语句，然后执行了`finally`中的语句。
　　但是到底有没有执行return呢？稍微改动一下代码就知道了，将`finally`中的`Console.ReadKey()`语句删除掉，然后运行程序可以发现，控制台窗口一闪而过，但是我们的代码最后是写了`Console.ReadKey()`暂停语句的，这就可以想到，整个执行的过程了：程序首先判断`if`条件成立，然后执行了其中的代码，执行到`return`的时候，去检查有没有`finally`语句，发现有，然后执行了`finally`中的代码，最后返回来执行了`if`中`return`语句。 所以我们看到了最后一行暂停语句没有起作用，因为执行完`finally`中的代码后直接return出去了。

```
    class Program
    {
        static void Main(string[] args)
        {
            int num = 20;
            try
            {
                if (num<10)
                {
                    Console.WriteLine("带有return语句的try块");
                    return;
                }
                else
                {
                    Console.WriteLine("不带return语句的try块");
                }
            }
            finally
            {
                Console.WriteLine("这是finally块中的输出！！");
            }
            Console.ReadKey();
        }
    }
```
　　这次将`num`的赋值为20，让程序执行`else`中的代码，然后看看执行结果：
![](/image/cSharp/cSharp24.png)

### 为异常寻找处理代码
　　当程序产生一个异常时，系统查看该程序是否为它提供了一个处理的代码。
　　■ 如果在try块内发生了异常，系统会查看是否有任何一个catch子句可能处理该异常。
　　■ 如果找到了适当的catch子句：
　　　　□ 该catch子句被执行
　　　　□ 如果有finally块，那么它也被执行

　　**更进一步**
　　如果异常在一个没有被try语句保护的代码段中产生，或者try语句没有匹配的异常处理程序(catch)，系统将不得不更进一步寻找匹配的处理代码，为此他会按顺序搜索调用栈，看看是否存在带相匹配处理代码的try块。
![](/image/cSharp/cSharp25.png)
　　在图的左边是代码的调用结构，在右边是调用栈。Method2被从Method1的try块内部调用，如果异常发生在Method2内的try块内部，系统会：
　　■ 首先，系统会查看Method2是否有能处理该异常的异常处理程序。
　　　　□ 如果有，Method2处理它，程序继续执行。
　　　　□ 如果没有，系统再延着调用栈找到Method1，寻找一个适当的处理代码。
　　■ 如果Method1有一个适当的catch子句，那么系统将：
　　　　□ 回到栈顶，那里是Method2。
　　　　□ 执行Method2的finally块，并把Method2弹出栈。
　　　　□ 执行Method1的catch子句和它的finally块。
　　■ 如果Method1没有适当的catch子句，系统会继续搜索调用栈。

　　**处理异常的一般过程**
![](/image/cSharp/cSharp26.png)

　　**搜索调用栈的示例**
```
    class Program
    {
        static void Main(string[] args)
        {
            MyClass mc = new MyClass();
            try
            {
                mc.A();
            }
            catch (DivideByZeroException e)
            {
                Console.WriteLine("Main方法中的catch子句~~");
            }
            finally
            {
                Console.WriteLine("Main方法中的finally子句~~");
            }
            Console.WriteLine("Main方法中的异常处理~~");
            Console.ReadKey();
        }
    }

    class MyClass
    {
        public void A()
        {
            try
            {
                B();
            }
            catch (NullReferenceException)
            {
                Console.WriteLine("A方法中的catch子句~~");
            }
            finally
            {
                Console.WriteLine("A方法中的finally子句~~");
            }
        }

        private void B()
        {
            int x = 10, y = 0;
            try
            {
                x /= y;
            }
            catch (IndexOutOfRangeException)
            {
                Console.WriteLine("B方法中的catch子句~~");
            }
            finally
            {
                Console.WriteLine("B方法中的finally子句~~");
            }
        }
    }
```
执行结果：
![](/image/cSharp/cSharp27.png)
　　**执行过程**：
　　⒈ Main调用A，A调用B，B遇到一个*DivideByZeroException*异常。
　　⒉ 系统检查B的catch段寻找匹配的catch子句。虽然它有一个*IndexOutOfRangeException*的子句但是没有*DivideByZeroException*的。
　　⒊ 系统然后延着调用栈向下移动并检查A的catch段，在那里发现A也没有匹配的catch子句。
　　⒋ 系统继续延续调用栈向下，并检查Main方法的catch子句部分，在那里它发现Main方法确实有一个*DivideByZeroException*的子句。
　　⒌ 尽管匹配的catch子句现在被定位了，但并不立即执行。相反，系统回到栈的顶端，执行B的finally子句，并把B从调用栈中弹出。
　　⒍ 系统移动到A，执行它的finally子句，并把A从调用栈中弹出。
　　⒎ 最后，Main方法匹配的catch子句被执行，接着是它的finally子句，然后执行流程在Mian方法的try语句结尾之后继续。
![](/image/cSharp/cSharp28.png)

### 抛出异常
　　可以使用`throw`语句使代码显示的引发一个异常。语法如下：
`throw ExceptionObject;`

```
    class Program
    {
        static void Main(string[] args)
        {
            string str = null;
            MyClass.ThrowMessage(str);
            Console.ReadKey();
        }
    }
    class MyClass
    {
        public static void ThrowMessage(string arg)
        {
            try
            {
                if (arg==null)
                {
                    ArgumentNullException ane = new ArgumentNullException("arg");
                    throw ane;
                }
            }
            catch (ArgumentNullException e)
            {
                Console.WriteLine("异常信息：{0}",e.Message);
            }
        }
    }
```
运行结果：
![](/image/cSharp/cSharp29.png)
　　这段代码定义了一个名为`ThorwMessage`的方法，它带一个`string`类型的参数，在try块内部，它首先做检查以确认该参数是不是null，如果是null就创建一个`ArgumentNullExceptuion`实例并抛出(throw)它。这个异常的实例也被catch语句捕获，并且获得该异常实例的属性。

### 不带异常对象的抛出
　　`throw`语句还可以不带异常对象使用，在catch块内部：
　　■ 这种形式重新抛出当前异常，系统继续他的搜索，为该异常寻找另外的处理代码。
　　■ 这种形式只能用在catch语句的内部。

```
    class Program
    {
        static void Main(string[] args)
        {
            string str = null;
            MyClass.ThrowMessage(str);
            Console.ReadKey();
        }
    }
    class MyClass
    {
        public static void ThrowMessage(string arg)
        {
            try
            {
                try
                {
                    if (arg == null)
                    {
                        ArgumentNullException ane = new ArgumentNullException("arg");
                        throw ane;
                    }
                }
                catch (ArgumentNullException e)
                {
                    Console.WriteLine("异常信息：{0}", e.Message);
                    throw;
                }
            }
            catch
            {
                Console.WriteLine("重新抛出了异常啦！！！！");
            }
        }
    }
```
运行结果：
![](/image/cSharp/cSharp30.png)
　　从运行结果中可以看出，catch中的`throw`再一次抛出异常，造成了顶层`try...catch`语句中catch代码触发，其中的语句最后被打印了出来。