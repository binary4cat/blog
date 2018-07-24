---
title: 泛型(generic)
date: 2015-05-22T16:17:54+08:00
tags: ["c#学习笔记"]
categories: ["c#"]
permalink: c#-generic
description: 泛型(generic)
---
## 什么是泛型
　　我们平常所用到的类型都是特定的类型——由编程语言或者BCL(基类库)、程序员自己定义的。为了让代码更加的灵活、解耦，我们可以将类的行为提取出来，使这些相似性的特征不仅可以用在当前的数据类型上，而且还可以扩展到其他的类型上，这样使类更加的有用。
　　泛型就是专门处理这种情况的，泛型就可以替代这种抽象的数据类型，泛型就相当于是类型的占位符。<!--more-->
### 示例：
　　假设我们声明了一个`MyIntStack`类，该类实现了一个int类型的栈，它允许我们把int类型的数据压栈和出栈。
```
class MyIntStack
{
    int StackPointer=0;
    int[] StackArray;
    
    public void Push(int x)
    {
        //压栈  
    }   
    public int pop()
    {
        //出栈
    }
}
```
　　假设我们需要一个float类型的值也具有相同的功能(压栈和出栈),我们需要重新编写针对float类型的代码：
```
class MyIntStack
{
    int StackPointer=0;
    float[] StackArray;
    
    public void Push(float x)
    {
        //压栈  
    }   
    public float pop()
    {
        //出栈
    }
}
```
　　※ 如果我们需要其他类型的数据也具有压栈和出栈的功能，就前面的做法，需要针对每种类型编写相似的代码，这些亢余代码不仅占据了额外的空间，而且不利于调试和维护。

## c#中的泛型
　　在c#2.0中，微软引入了泛型特性。泛型允许我们声明类型参数化的代码，我们可以使用“**类型占位符**”写代码，然后在创建类的实例的时候提供真实的类型。
![](/image/cSharp/cSharp65.png)
　　★ c#提供了5种泛型：类、结构、接口、委托、方法。其中前四个是类型，而方法是成员。

### 栈的示例
　　我们接着前面的代码，使用泛型处理代码亢余的情况。
```
class MyStack<T>
{
    int StackPointer=0;
    T[] StackArray;
    public void Push(T x)
    {
        //压栈
    }
    public T Pop()
    {
        //出栈
    }
}
```
　　以上是一个泛型类的声明，“T”代表类型的占位符(也可以是其他字符)，在实例化时，每一个“T”都会被编译器替换为实际类型。

## 泛型类
　　创建和使用常规的非泛型的类有两个步骤：声明类、创建类的实例。但是泛型类不是实际的类，而是类的模板，创建时需要：
　　■ 在某些类型上使用占位符来声明一个泛型类。
　　■ 为占位符提供真实类型，这样就有了真实类型的定义，填补了所有的“空缺”。
　　■ 从“填空后”的类定义创建实例。
![](/image/cSharp/cSharp66.png)

### 声明泛型类
　　声明泛型类：
　　■ 在类名之后放置一组尖括号。
　　■ 在尖括号中用逗号分隔的占位符字符串来表示希望提供的类型。这叫做*类型参数(type parameter)*。
　　■ 在泛型类声明的主体中使用类型参数来表示应该被替代的类型。
```
//类型参数
class SomeClass <T1,T2>
{
    //通常在这些位置使用泛型类型
    public T1 SomeVar = new T1();
    public T2 OtherVar = new T2();
}
```

### 创建构造类型
　　我们不能直接从泛型类型创建类对象。
　　首先，我们需要告诉编译器使用哪些真实的类型来替代占位符(类型参数)，编译器获取这些真实类型并从它创建一个真实的类型对象。
　　★ 要从泛型类构建类类型，列出类名字并在尖括号中提供真实类型来替代类型参数，要替代类型参数的真实类型叫做*类型实参(type argument)*。
![](/image/cSharp/cSharp67.png)
　　★ 为泛型类提供了类型实参，替换泛型类主体中的相应的类型参数，这时相当于产生了一个构造类型，从这个构造类可以创建真实类型的实例了。
![](/image/cSharp/cSharp68.png)
　　类型参数和类型实参的区别：
![](/image/cSharp/cSharp69.png)

### 创建变量和实例
```
//普通类对象的创建
MyNonGenClass myNGC = new MyNonGenClass();

//泛型类对象的创建吗，使用short和int类型进行实例化。
SomeClass<short,int> mySc1 = new SomeClass<short,int>();

//和第二行代码一样，区别是使用var关键字使编译器使用类型。
var mySc2 = new SomeClass<short,int>();
```

　　泛型类也可以引用和实例分开创建。
```
//泛型类声明
class SomeClass <T1,T2>
{
    public T1 SomeVar;
    public T2 OtherVar;
}

SomeClass<short,int> myInst; //创建引用
myInst = new SomeClass<short,int>();  //创建实例
```
　　■ `SomeClass<short,int> myInst`这句代码在栈上为`myInst`分配了一个引用，值是null。
　　■ `myInst = new SomeClass<short,int>()`这句代码在堆上分配实例，并且把引用赋值给变量。

　　★ 可以从同一个泛型类型构建出不同的类类型。
```
class SomeClass <T1,T2>
{
    public T1 SomeVar;
    public T2 OtherVar;
}
class Program
{
    static void Main(string[] args)
    {
        //从同一个泛型类型构建出不同的类对象
        var class1 = new SomeClass<short,int>(); 
        var class2 = new SomeClass<int,long>();
    }
}
```
![](/image/cSharp/cSharp70.png)


## 类型参数的约束
　　有时候我们并不能让任何类型的类型实参去匹配类型参数，也就是说需要取代“T”占位符的参数类型有时候需要有一些限制，并不是所有的代码中都需要所有的数据类型。
　　★ 要让泛型变的更加准确，我们需要提供额外的信息让编译器直达参数可以接受哪些类型，这些额外的信息叫做**约束(constrain)**，只有符合约束的实参才能用于类型参数。

### where子句
　　约束使用where子句列出：
　　■ 每一个有约束的类型参数有自己的where子句。
　　■ 如果形参有多个约束，它们在where子中使用逗号分隔。
![](/image/cSharp/cSharp71.png)
　　where子句要点：
　　■ 它们在类型参数列表的关闭尖括号之后列出。
　　■ 它们不使用逗号或其他符号分隔。
　　■ 它们可以以任何次序列出。
![](/image/cSharp/cSharp72.png)

### 约束类型和次序
![](/image/cSharp/cSharp73.png)
　　where子句可以以任何次序列出，但是where子句中的约束却必须有特定的顺序。
　　■ 最多只能由一个主约束，而且必须放在第一位。
　　■ 可以有任意多的*InterfaceName*约束。
　　■ 如果存在构造函数约束(*new()*)，则必须放在最后。
![](/image/cSharp/cSharp74.png)
**示例**：
```
class SortedList<S>
        where S:IComparable<S>
    {
        ...
    }
    
class Linkedist<M,N>
        where M:IComparable<S>
        where N:ICloneable
    {
        ...
    }
    
class MyDictionary<KeyType,ValueType>
        where KeyType:IEnumerable,new()
    {
        ...
    }
```

## 泛型结构
　　与泛型类相似，泛型结构可以有类型参数和约束。**泛型结构的规则和条件与泛型类是一样的**。
```
    class Program
    {
        static void Main(string[] args)
        {
            var intData = new ShowData<int>(10);
            var stringData = new ShowData<string>("struct");
            Console.WriteLine(intData.Data);
            Console.WriteLine(stringData.Data);
            Console.ReadKey();
        }
    }

    struct ShowData<T>
    {
        public T Data { get; set; }
        public ShowData (T value)
        {
            Data = value;
        }

    }
```
运行结果：
![](/image/cSharp/cSharp78.png)

## 泛型接口
　　泛型接口允许我们编写*参数*和*成员返回类型*是*泛型类型参数*的接口。

### 声明泛型接口示例
```
//参数和成员的返回类型都是泛型T
interface IMyIfc<T>  
{
    T ReturenIt(T inValue);
}

//泛型类实现泛型接口，非泛型类也可实现泛型接口
class Simple<S> : IMyIfc<S>  
{
    public S ReturnIt(S inValue)
    {
        return inValue;
    }
}

class Program
{
    static void Main(string[] args)
    {
        //两种不同的参数类型
        var trivInt = new Simple<int>();
        var trivString = new Simple<string>();
        
        Console.WriteLine(trivInt.ReturnIt(5));
        Console.WriteLine(trivString.ReturnIt("泛型接口"));
    }
}
```

###　使用泛型接口示例
　　■ 与其他的泛型相似，同一个接口，实现了不同*类型参数*就是不同的接口(比如`IIfc<int>`和`IIfc<string>`虽然是同一个接口，但是由于类型参数不同，就是不同的接口)。
　　■ 可以在非泛型类中实现泛型接口。
```
interface IMyIfc<T>
{
    T ReturnIt(T inValue);
}

//源于同一个的泛型接口的两个不同接口
class Simple : IMyIfc<int>,IMyIfc<string>
{
    //实现int类型接口
    public int ReturnIt(int inValue)  
    {
        return inValue;
    }
    //实现string类型接口
    public string ReturnIt(string inValue)
    {
        return inValue;
    }
}
class Program
{
    static void Main(string[] args)
    {
        //两种不同的参数类型
        var trivInt = new Simple<int>();
        var trivString = new Simple<string>();
        
        Console.WriteLine(trivInt.ReturnIt(5));
        Console.WriteLine(trivString.ReturnIt("泛型接口"));
    }
}
```

### 泛型接口的实现必须唯一
　　在实现泛型接口时，必须确定的是不能让一个类去实现两个相同的接口，这是必须避免发生的，以下代码展示了这种情况的例子：
```
interface IMyIfc<T>
{
    T ReturnIt(T inValue);
}

//错误的代码，因为如果泛型“S”也是int类型，那么就相当于Simple类实现了两个相同的IMyIfc接口。
class Simple<S> : IMyIfc<int>,IMyIfc<S>
{
    //实现第一个接口
    public int ReturnIt(int inValue)
    {
        return inValue;
    }
    
    //实现第二个接口，如果它是int类型，就会出现错误异常
    public S ReturnIt(S inValue)
    {
        return inValue;
    }
}
```
　　**泛型接口的名字不会和非泛型冲突，比如**`IMyIfc<T>`**和**`IMyIfc`**是不会有命名冲突的。**

## 泛型委托
![](/image/cSharp/cSharp79.png)
　　■ 要声明泛型委托，在委托名称和委托参数列表之间的尖括号中放类型参数列表。
　　■ 这里有两个参数列表，委托形参列表和类型参数列表
　　■ 类型参数的范围包括：
　　　　□ 返回值
　　　　□ 形参列表
　　　　□ 约束子句
```
delegate void MyDeledate<T>(T value);   //泛型委托

class Simple
{
    static public void PrintString(string s)  //方法匹配委托
    {
        Console.WriteLine(s);
    }
    static public void PrintUpperString(string s)  //方法匹配委托
    {
        Console.WriteLine(s.ToUpper());
    }
}

class Program
{
    static void Main(string[] args)
    {
        var myDel = new MyDeledate<string>(Simple.PrintString);  //创建委托实例
        myDel += Simple.PrintUpperString;   //添加方法
        myDel("generic_delegate");            //调用委托
        Console.ReadKey();
    }
}
```
运行结果：![](/image/cSharp/cSharp80.png)


## 泛型方法
　　与其他泛型不一样的是，方法是成员，不是类型。
　　**泛型方法可以在泛型和非泛型类以及结构和接口中声明。**
![](/image/cSharp/cSharp81.png)

### 声明泛型方法
　　■ 泛型方法和泛型委托一样，有两个参数列表：方法参数列表、类型参数列表。
![](/image/cSharp/cSharp82.png)

### 调用泛型方法
　　要调用泛型方法，应该在方法调用时提供类型实参：
```
MyMethod<short,int>();
MyMethod<int,long>();
```
有两个实例的泛型方法：
![](/image/cSharp/cSharp83.png)

### 推断类型
　　如果我们为泛型方法传入了参数，编译器可以根据传入的参数判断类型参数的类型，因此可以省略类型参数，形成简写形式：
```
//类型参数列表和方法参数列表都是“T”类型
public void MyMethod <T> (T myVal)
{
    ...
}

int MyInt=5;
MyMethod <int> (MyInt);  //两个类型都是int类型的

//可以简写成
MyMethod (MyInt);
```

### 泛型方法的示例
```
    class Simple          //非泛型类
    {
        static public void ReverseAndPrint<T>(T[] arr)  //泛型方法
        {
            Array.Reverse(arr);
            foreach (T item in arr)
            {
                Console.Write("{0}，",item.ToString());
            }
            Console.WriteLine("");
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            var intArray = new int[] { 3, 5, 7, 9, 11 };
            var stringArray = new string[] { "壹", "贰", "叁" };
            var doubleArray = new double[] { 1.11, 2.33, 4.44 };

            Simple.ReverseAndPrint<int>(intArray);   //调用方法
            Simple.ReverseAndPrint(intArray);        //简写
            
            Simple.ReverseAndPrint<string>(stringArray);   //调用方法
            Simple.ReverseAndPrint(stringArray);        //简写

            Simple.ReverseAndPrint<double>(doubleArray);   //调用方法
            Simple.ReverseAndPrint(doubleArray);        //简写

            Console.ReadKey();
        }
    }
```
运行结果：
![](/image/cSharp/cSharp84.png)
