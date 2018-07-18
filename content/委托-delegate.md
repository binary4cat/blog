---
title: 委托(delegate)
date: 2015-05-20T16:47:52+08:00
tags: c#学习笔记
categories: c#
permalink: c#-delegate
description: 委托(delegate)
---
### 什么是委托
　　**委托**是一个包含具有相同签名和返回值类型的有序方法列表。
　　■ 方法的列表称为**调用列表**(invocation list)
　　■ 当委托被调用时，它调用列表中的每一个方法。
![](/image/cSharp/cSharp31.png)<!--more-->
　　包含单个方法的委托和C++的函数指针相似，然而，与函数指针不同的是，委托是面向对象的，并且是类型安全的(type-safe)的。
　　
　　**“调用列表”中的方法**：
　　由委托保存的方法可以来自任何类或者结构，只要他们同时匹配委托的如下两点：
　　■ 返回值
　　■ 签名(包括ref和out修饰符)
　　调用列表中的方法可以是实例方法或者静态方法。

### 声明委托类型
　　委托是一种数据类型，就好像类是类型一样。与类相同，委托类型必须在被用来创建变量以及类型的对象之前进行声明，才可以使用。
![](/image/cSharp/cSharp32.png)
　　委托类型的声明看上去和方法的声明比较相似，有返回值类型和签名，**返回值类型和签名指定了委托接受的方法的形式**。
　　上图中的代码声明了*MyDel*类型的委托。声明指定了这种类型的委托只会接受没有返回值(void)并且有单个int参数的方法。
![](/image/cSharp/cSharp33.png)

　　**委托类型声明与方法声明的两个不同方面**：
　　■ 以*delegate*关键字开头
　　■ 没有方法主体

### 创建委托对象
　　**委托是引用类型**，因此有引用和对象。我们可以声明变量并创建类型的对象。
![](/image/cSharp/cSharp34.png)

　　有两种创建委托对象的方式：
　　第一种是使用带*new*运算符的对象创建表达式。
![](/image/cSharp/cSharp35.png)
　　第二种是使用快捷语法，直接使用方法赋值。使用快捷语法方法名称和其相对应的委托类型之间有隐式转换。
![](/image/cSharp/cSharp36.png)
```
delegate void MyDel(int x);       //声明委托类型
MyDel delVar,dVar;                //创建两个委托变量

delVar= new MyDel(myInstObj.MyM1);//创建委托并保存引用
dVar =new MyDel(SClass.OtherM2);  //创建委托并保存引用
```
![](/image/cSharp/cSharp37.png)
当然也可以这样创建：
```
MyDel delVar=new MyDel(myInstObj.MyM1);
MyDel dVar= new MyDel(SClass.OtherM2);
```
或者使用快捷语法：
```
MyDel delVar=myInstObj.MyM1;
MyDel dVar= SClass.OtherM2;
```

### 委托赋值
　　由于委托是引用类型，我们可以通过给它赋值来改变包含在委托变量中的引用，旧的委托对象会被GC回收。
```
MyDel delVar;
delVar = myInstObj.MyM1;
//重新赋值
delVar = SClass.OtherM2;
```
![](/image/cSharp/cSharp38.png)

### 组合委托
　　上面描述的委托的调用列表中都只有一个方法，委托可以使用**+**运算符来“组合”。这个运算最终会创建一个新的委托，新委托的调用列表是两个被操作委托调用列表的组合。
　　**组合委托**的操作数委托没有被修改，当组合委托的操作发生时，参与运算的委托都没有发生改变，只是创建了一个新的委托。**委托是恒定的，委托对象被创建后就不会再被改变。**
```
MyDel delA = myInstObj.MyM1;
MyDel delB = SClass.OtherM2;

MyDel delC = delA + delB;    //组合委托会组合操作数委托的调用列表
```
![](/image/cSharp/cSharp39.png)

### 为委托增加和移除方法
　　**增加方法**：
　　虽然委托是不可变的，但是c#提供了看上去可以为委托增加方法的语法，我们可以通过使用“**+=**”运算符来为委托增加方法或者另一个委托。
```
MyDel delVar = inst.MyM1;    //创建委托并初始化
delVar += SCl.m3;            //增加方法
delVar += X.Act;             //增加方法
```
　　**在使用+=运算符时，实际上是创建了个新的委托，新委托的调用列表是左边的委托加上右边要添加的方法。**
![](/image/cSharp/cSharp40.png)

　　**移除方法**：
　　可以使用**-=**运算符从委托移除方法。
`delVar -= SCl.m3;    //从委托移除方法`
　　与为委托增加方法一样，移除方法其实就是创建了一个新的委托。新的委托是就委托的副本——只是没有了已经移除方法的引用。
　　移除委托时需要注意：
　　■ 如果在调用列表中的方法有多个实例，**-=**运算符将从列表最后还是搜索，并且移除第一个与方法匹配的实例。
　　■ 试图删除委托中不存在的方法没有效果。
　　■ 试图调用空委托会抛出异常。
　　■ 我们可以通过把委托和null进行比较来判断委托的调用列表是否为空。**如果调用列表为空，则委托是null**。  

### 调用委托
　　可以向调用方法一样简单的调用委托。
```
delegate void MyDel (int x);
MyDel delVar =inst.MyM1;
delVar += SCl.m3;
delVar += X.Act;
   ....
delVar(55);    //调用委托
   ....
```
![](/image/cSharp/cSharp41.png)
　　当委托被调用时，它使用被调用时的相同参数来调用调用列表中的每一个方法。
　　如果在调用列表里有好多个相同的方法，当委托被调用时，每次在列表中遇到这个方法时都会调用一次。
　　

**调用不带返回值的委托示例**：
```
using System;

namespace Delegate
{
    //定义一个没有参数和返回值的委托
    delegate void PrintFunction();
    class Program
    {
        static void Main(string[] args)
        {
            Test t = new Test();
            PrintFunction pf; //创建一个空委托
            pf = t.Print1;    //初始化该委托

            //给委托增加方法
            pf += Test.Print2;
            pf += t.Print1;

            //调用委托
            if (pf != null)
                pf();
            else
                Console.WriteLine("该委托为空！");
            Console.ReadKey();
        }
    }
    class Test
    {
        public void Print1()
        {
            Console.WriteLine("Print1--实例方法");
        }
        public static void Print2()
        {
            Console.WriteLine("Print2--静态方法");
        }
    }
}
```
运行结果：
![](/image/cSharp/cSharp42.png)
　　在上面的代码中，我们给委托增加了三个方法，其中有两个方法(Print1)是重复的，从运行结果可以看出，按照顺序，`Print1`被调用了2次。
　　**调用委托其实就是调用了委托的调用列表中的所有方法。**

**调用带返回值的委托示例**：
　　如果委托有返回值，并且在调用列表中有一个以上的方法存在，那么：
　　■ 调用列表中的最后一个方法的返回值就是委托调用返回的值。
　　■ 调用列表中所有其他方法的返回值都会被忽略。
```
using System;

namespace Delegate2
{
    //创建一个具有返回值，没有参数的委托
    delegate int MyDel();
    class Program
    {
        static void Main(string[] args)
        {
            Test t = new Test();
            MyDel mDel = t.Add2;
            mDel += t.Add3;
            mDel += t.Add2;
            Console.WriteLine("值为：{0}",mDel());
            Console.ReadKey();
        }
    }
    class Test
    {
        int value = 5;
        public int Add2()
        {
            value += 2;
            return value;
        }
        public int Add3()
        {
            value += 3;
            return value;
        }
    }
}
```
运行结果：
![](/image/cSharp/cSharp43.png)
　　■ 上面代码中的委托添加了三个方法，分别是`Add2`、`Add3`、`Add2`。
　　■ 在最后打印的时候调用了委托，执行的过程是：![](/image/cSharp/cSharp44.png)

**调用带引用参数的委托示例**
　　如果委托有引用参数，在调用委托列表中的下一个方法时，参数的新值(不是初始值)会传给下一个方法。以此类推。
```
using System;

namespace Delegate3
{
    delegate void MyDel(ref int x);
    class Program
    {
        static void Main(string[] args)
        {
            Test t = new Test();
            MyDel mDel = t.Add2;
            mDel += t.Add3;
            mDel += t.Add2;
            int x = 5;
            mDel(ref x);
            Console.WriteLine("值为：{0}",x);
            Console.ReadKey();
        }
    }
    class Test
    {
        public void Add2(ref int x)
        {
            x += 2;
        }
        public void Add3(ref int x)
        {
            x += 3;
        }
    }
}
```
运行结果：
![](/image/cSharp/cSharp45.png)
　　■ 引用参数是按顺序在调用方法中一次传递的：![](/image/cSharp/cSharp46.png)


### 匿名方法
　　如果一个方法在调用列表中，或者说在委托的调用中只会被使用一次，那么则没有必要创建一个独立的具名方法，可以使用匿名方法。
　　**匿名方法**(anonymous metfod)是在初始化委托是内联声明的方法。

**具名方法代码示例：**
```
using System;

namespace Delegate4
{
    delegate int MyDel(int x);
    class Program
    {
        static void Main(string[] args)
        {
            MyDel md = Add;

            Console.WriteLine("具名方法的输出：{0}",md(5));
            Console.WriteLine("具名方法的输出：{0}",md(6));
            Console.ReadKey();
        }
        public static int Add(int x)
        {
            return x += 20;
        }
    }
}
```
运行结果：![](/image/cSharp/cSharp47.png)
**匿名方法代码示例：**
```
using System;

namespace Delegate5
{
    delegate int MyDel(int x);
    class Program
    {
        static void Main(string[] args)
        {
            MyDel md = delegate (int x)
              {
                  return x += 20;
              };
            Console.WriteLine("匿名方法的输出：{0}",md(5));
            Console.WriteLine("匿名方法的输出：{0}",md(6));
            Console.ReadKey();
        }
    }
}
```
运行结果：![](/image/cSharp/cSharp48.png)
　　可以看到两种方式都得到了相同的计算结果。**一个方法只会被使用一次，这个方法初始化委托时可以使用匿名方法的形式。**

#### 在什么地方使用匿名方法：
　　■ 声明委托变量时作为初始化表达式
　　■ 组合委托是在赋值语句的右边
　　■ 为委托增加事件时在赋值语句的右边

#### 匿名方法的语法：
![](/image/cSharp/cSharp49.png)
　　■ **delegate**类型关键字。
　　■ 参数列表，如果语句块没有使用任何参数则可以省略。
　　■ 语句块，它包含了匿名方法的代码。
　　
##### 1、返回类型：
　　**匿名方法不会显示的声明返回值**。但是匿名方法的实现本身的行为必须一个类型上与委托返回值类型想匹配的值。**如果委托有void类型的返回值，那么匿名方法就不能有返回值**。
例如：
```
delegate int MyDel(int x);     //委托类型的返回值类型是int
static void Main(string[] args)
{
    MuDel md = delegate (int x)
            {
                return x + 20; //匿名方法也必须返回一个int类型的值
            };
}
```

##### 2、参数
　　除了参数是数组的情况，匿名方法的参数列表必须在三个方面匹配委托：
　　■ 参数数量
　　■ 参数类型
　　■ 修饰符
　　
　　**我们可以在委托的参数列表不包含任何out参数，并且匿名方法不使用任何参数的情况下，通过圆括号为空或者直接省略圆括号来简化匿名方法的参数列表。**
```
delegate void MyDel(int x); 

//调用委托
MyDel md = delegate      //省略的参数列表
        {
            Console.WriteLine("简化匿名方法！");  
        };
```

##### 3、params参数
　　如果委托声明的参数列表中包含了params参数，那么使用匿名方法时，匿名方法的参数列表就会忽略params关键字。
```
delegate void MyDel(int x,params int[] y);   //在委托类型的声明中使用了params关键字

MyDel md = delegate(int x,int[] y)    //在匹配的匿名方法中忽略了params关键字
        {
           ......
        };
```

### 变量和参数的作用域
　　匿名方法的参数以及声明在匿名方法内部的局部变量的作用域限制在匿名方法的主体之内。
![](/image/cSharp/cSharp50.png)
##### 1、外部变量
　　匿名方法可以访问它的外围作用域的局部变量和环境。
　　■ 外围作用域的变量叫做**外部变量**(outer variable)。
　　■ 用在匿名方法实现代码中的外部变量称为**被方法捕获**(captured)。
![](/image/cSharp/cSharp51.png)

##### 2、被方法捕获变量的生命周期扩展
![](/image/cSharp/cSharp52.png)
　　■ 局部变量x在块中声明和初始化
　　■ 委托mDel被匿名方法初始化，该匿名方法捕获了外部变量x
　　■ 当x离开块区域时，引用x的值会发生编译错误
　　■ 但是，委托mDel中的匿名方法在它的环境中保留了变量x，并且在mDel被调用时可以输出x的值。


## Lambda表达式
　　c#2.0中引入了匿名方法，它允许我们在创建或者为委托增加方法时包含小段内联代码。
　　c#3.0中引入了lambda表达式，简化了匿名方法的语法。
　　
　　在匿名方法的语法中，delegate关键字是有点多余的，因为编译器已经知道我们在将方法赋值给委托，我们可以把匿名方法转换为lambda表达式。
　　■ 删除delegate关键字
　　■ 在参数列表和匿名方法主体之间放lambda运算符“**=>**”
```
MyDel md = delegate(int x)    {return x + 1};   //匿名方法
MyDel md =         (int x) => {return x + 1};   //lambda表达式
```
　　编译器通过推断允许我们更进一步简化lambda表达式：
```
MyDel md  = delegate(int x)    {return x + 1};   //匿名方法
MyDel md2 =         (int x) => {return x + 1};   //lambda表达式
MyDel md3 =             (x) => {return x + 1};   //lambda表达式
MyDel md4 =              x  => {return x + 1};   //lambda表达式
MyDel md5 =              x  =>         x + 1 ;   //lambda表达式
```
　　■ 编译器还可以从委托的声明中知道委托参数的类型，因此lambda表达式允许我们省略类型参数，如上面代码中`md3`的赋值代码所示。
　　　　□ 带有类型的参数列表称为显式类型
　　　　□ 省略类型的参数列表称为隐式类型
　　■ 如果只有一个隐式类型的参数，我们可以省略周围的圆括号，如上面代码中的`md4`赋值代码所示。
　　■ **lambda表达式允许表达式的主体是语句块或表达式**。如果语句块包含了一个返回语句(return)，我们可以将语句块中的return关键字省略，直接写表达式。如`md5`的赋值代码所示。

　　有关lambda表达式的参数列表的要点如下：
　　■ lambda表达式参数列表中的参数必须在参数数量、类型和位置上与委托相匹配。
　　■ 表达式的参数列表中的参数不一定需要包含类型(比如隐式类型)，除非委托有`ref`或者`out`参数，此时声明参数类型是必须的(比如显式类型)。
　　■ 如果只有一个参数，并且是隐式类型，周围的圆括号可以被省略，否则圆括号就是必须写上的。
　　■ 如果没有参数，则必须使用一组空的圆括号代替。