---
title: Java基础：final&匿名类&代码块
date: 2017-4-23 15:23:27
categories: Java
tags: Java基础
permalink: Java-final-class-code
description: Java基础：final&匿名类&代码块
photos: http://ww1.sinaimg.cn/large/c55a7aeely1fmbwbz5s71j20et08cjra.jpg
---
<!-- TOC -->

- [final关键字](#final关键字)
- [final修饰类义](#final修饰类义)
- [final修饰方法](#final修饰方法)
- [final修饰局部变量](#final修饰局部变量)
- [final修饰成员变量](#final修饰成员变量)
- [匿名对象](#匿名对象)
- [内部类](#内部类)
- [成员内部类的调用格式](#成员内部类的调用格式)
- [成员内部类的同名变量调用](#成员内部类的同名变量调用)
- [局部内部类](#局部内部类)
- [代码块](#代码块)

<!-- /TOC -->
### final关键字
- 概述
	- 继承的出现提高了代码的复用性，并方便开发。但随之也有问题，有些类在描述完之后，不想被继承，或者有些类中的部分方法功能是固定的，不想让子类重写。可是当子类继承了这些特殊类之后，就可以对其中的方法进行重写，那怎么解决呢？
	- 要解决上述的这些问题，需要使用到一个关键字final，final的意思为最终，不可变。
	- final是个修饰符，它可以用来修饰类，类的成员，以及局部变量。	
<!--more-->
### final修饰类义
- `final` 修饰类
	- `final`修饰类不可以被继承，但是可以继承其他类。
	```Java
	class Obj {}
	final class Parent extends Obj{} //可以继承Obj类
	class Sub extends Parent{} //不能继承Parent类
	```


### final修饰方法
- final修饰方法
	- final修饰的方法不可以被覆盖,但父类中没有被final修饰方法，子类覆盖后可以加final。
```Java	
class Parent {
	// final修饰的方法，不可以被覆盖，但可以继承使用
	public final void method1(){}
	public void method2(){}
}
class Sub extends Parent {
	//重写method2方法
	public final void method2(){}
}
```


### final修饰局部变量
- 修饰基本数据类型变量
	- final修饰的变量称为常量，这些变量只能赋值一次
	```Java
	final int i = 20;
	i = 30; //赋值报错，final修饰的变量只能赋值一次
	```


- 修饰引用数据类型
	-引用类型的变量值为对象地址值，地址值不能更改，但是地址内的对象属性值可以修改
```Java
final Person p = new Person();
Person p1 = new Person();
p = p1; //错误！ final修饰的变量p，所记录的地址值不能改变
p.name = "zhangsan";//可以更改p对象中name属性值
//p不能为别的对象，而p对象中的name或age属性值可更改。
```


### final修饰成员变量
- 修饰成员变量
	- 修饰成员变量，需要在创建对象前赋值，否则报错。(当没有显式赋值时，多个构造方法均需要为其赋值。)
```Java
class Demo {
	//直接赋值
	final int m = 100;
	//final修饰的成员变量，需要在创建对象前赋值，否则报错。
	final int n; 
	public Demo(){
		//可以在创建对象时所调用的构造方法中，为变量n赋值
		n = 2016;
	}
}
```


### 匿名对象
- 匿名对象的概述
	- 匿名对象是指创建对象时，只有创建对象的语句，却没有把对象地址值赋值给某个变量。

```Java
public class Person{
	public void eat(){
		System.out.println();
	}
}
//创建一个普通对象
Person p = new Person();
//创建一个匿名对象
new Person();
```


### 内部类
- 内部类的概述
	- 将类写在其他类的内部，可以写在其他类的成员位置和局部位置，这时写在其他类内部的类就称为内部类。
	- 其他类也称为外部类。
- 什么时候使用内部类
	- 在描述事物时，若一个事物内部还包含其他可能包含的事物，比如在描述汽车时，汽车中还包含这发动机，
	- 这时发动机就可以使用内部类来描述。
```Java
class Car { //外部类
	class Engine { //内部类
	}
}
```


- 内部类的分类
	- 内部类分为成员内部类与局部内部类。
	- 我们定义内部类时，就是一个正常定义类的过程，同样包含各种修饰符、继承与实现关系等。
	- 在内部类中可以直接访问外部类的所有成员。

### 成员内部类的调用格式
- 成员内部类，定义在外部类中的成员位置。与类中的成员变量相似，可通过外部类对象进行访问
```Java
class External { 
	public class Internal {
		//TODO
	}
}
```


- 访问方式
	- 外部类名.内部类名 变量名 = new 外部类名().new 内部类名();
	```Java
	External.Internal ei = new External().new Internal();
	```


### 成员内部类的同名变量调用
```Java
public class Outer {
	int i  = 1;
	class Inner {
		int i  = 2;
		public void inner(){
			int i = 3;
			System.out.println(Outer.this.i);
		}
	}
}
```


### 局部内部类
- 局部内部类，定义在外部类方法中的局部位置。与访问方法中的局部变量相似，可通过调用方法进行访问.
```Java
class External { 
	Public Void Function(String name) {
		class Internal {
			//TODO
		}
	}
}
```

- 访问方式
```Java
//外部类，聚会
class Party {
	public void puffBall(){// 吹气球方法
		class Ball {// 内部类，气球
			public void puff(){
				System.out.println("气球膨胀了");
			}
		}
		//创建内部类对象，调用puff方法(只能通过这种方法)
		new Ball().puff();
	}
}
//访问内部类
public static void main(String[] args) {	
	//创建外部类对象
	Party p = new Party();
	//调用外部类中的puffBall方法
	p.puffBall();
}
```

### 代码块
- 概述:
	- 程序中用大括号括起来的代码叫代码块
- 分类
	- 局部代码块
	- 构造代码块
	- 静态代码块  
	- 同步代码块
	 
- 局部代码块:
	- 局部代码块是定义在方法或语句中
	- 特点：
		- 以`{}`划定的代码区域，此时只需要关注作用域的不同即可，方法和类都是以代码块的方式划定边界的
		- 局部代码块作用:可以限定变量的声明周期。
```Java
class Test{
	public static void main(String[] args)	{
		//代码块
		{
			int x = 1;
			System.out.println("普通代码块" + x);
		}
		int x = 99;
		System.out.println("代码块之外" + x);
	}
}
/*
*结果
*普通代码块1
*代码块之外99
*/
```

- 构造代码块
	- 构造代码块是定义在类中成员位置的代码块
	- 特点：
		- 优先于构造方法执行，构造代码块用于执行所有对象均需要的初始化动作
		- 每创建一个对象均会执行一次构造代码块
```Java
public class Person {
	private String name;
	private int age;
	//构造代码块
	{
		System.out.println("构造代码块执行了");
	}
	Person(){
		System.out.println("Person无参数的构造函数执行");
	}
	Person(int age){
		this.age = age;
		System.out.println("Person（age）参数的构造函数执行");
	}
}
class PersonDemo{
	public static void main(String[] args)	{
		Person p = new Person();
		Person p1 = new Person(23);
	}
}
```
- 静态代码块
	- 静态代码块是定义在成员位置，使用static修饰的代码块。
	- 特点：
		- 它**优先于主方法执行、优先于构造代码块执行**，当以任意形式第一次使用到该类时执行。
		- 该类不管创建多少对象，静态代码块只执行一次。
		- 可用于给静态变量赋值，用来给类进行初始化。\
```Java
public class Person {
	private String name;
	private int age;
	//静态代码块
	static{
		System.out.println("静态代码块执行了");
	}
}
```