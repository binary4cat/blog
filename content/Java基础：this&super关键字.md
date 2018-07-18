---
title: Java基础：this&super关键字
date: 2017-4-10 19:23:27
categories: Java
tags: Java基础
permalink: Java-this&super-keyword
description: Java基础：this&super关键字
photos: http://ww1.sinaimg.cn/large/c55a7aeely1fmbwbz5s71j20et08cjra.jpg
---
### 为什么要有构造方法？
- 构造方法的作用: 
	- 在new的同时给成员变量赋值,给对象属性进行初始化。
			
### 构造方法的定义和运行特点
- 构造方法定义
构造方法的格式：
```Java
修饰符 构造方法名(参数列表)
{
}
```
<!--more-->
- 构造方法的体现：
	- 构造方法没有返回值类型。也不需要写返回值。因为它是为构建对象的，对象创建完，方法就执行结束。
	- 构造方法名称必须和类型保持一致。
	- 构造方法没有具体的返回值。
- 构造方法运行特点:
	- 在new 对象的时候自动调用执行。



### 默认添加的构造方法
- 每一class类都必须有一个构造方法，构造方法不写也有。
- 编译的时候，javac，系统会自动检查类中是否有构造方法，如果没有编译器就会自动添加一个构造方法
		
### 构造方法的内存
- 内存加载的过程
	- 例如有一个Person类, 创建Person 对象new Person()
		1. 首先会将main方法压入栈中，执行main方法中的 new Person(23,"张三"); 
		2. 在堆内存中分配一片区域，用来存放创建的Person对象，这片内存区域会有属于自己的内存地址（0x0001）。然后给成员变量进行默认初始化（name=null，age=0）。
		3. 执行构造方法中的代码（age = a ; name = nm;）,将变量a对应的23赋值给age，将变量nm对应的“张三”赋值给name，这段代码执行结束后，成员变量age和name的值已经改变。执行结束之后构造方法弹栈，Person对象创建完成。将Person对象的内存地址0x0001赋值给p。
		```Java
		public class Person{
			public String name;
			public int age;
			public Person(int a,String nm){
				age = a;
				name = nm;
			}
		}
		.......
		public static void main(String[] args){
			Person p = new Person(23,"张三");
		}
		```


### 构造方法的重载			
- 构造方法的细节：
	- 一个类中可以有多个构造方法，多个构造方法是以重载的形式存在的
	- 构造方法是可以被private修饰的，作用：其他程序无法创建该类的对象。
```Java
class Person {
	private int age;
	private String name;
		
	// 私有无参数的构造方法，即外界不能通过new Person();语句创建本类对象
	private Person() {
	}		
	// 多个构造方法是以重载的形式存在
	Person(int a) {
		age = a;
	}		
	Person(String nm, int a) {
		name = nm;
		age = a;
	}
}
```

### this在构造方法调用的内存
```Java
class Person {
	private int age;
	private String name;

	Person() {}
	Person(String nm) {
		name = nm;
	}
	Person(String nm, int a) {
		this(nm);
		age = a;
	}
}

class PersonDemo {
	public static void main(String[] args) {
		Person p = new Person("张三", 23);
	}
}
```
	
- 构造方法调用的过程
	1. 先执行main方法，main方法压栈，执行其中的new Person(“张三”,23);
	2. 堆内存中开辟空间，并为其分配内存地址0x001，，紧接着成员变量默认初始化（name=null  age = 0）；
	3. 拥有两个参数的构造方法（Person（String nm , int a））压栈，在这个构造方法中有一个隐式的this，因为构造方法是给对象初始化的，那个对象调用到这个构造方法，this就指向堆中的那个对象。
	4. 由于Person（String nm , int a）构造方法中使用了this(nm);构造方法Person(String nm)就会压栈，并将“张三”传递给nm。在Person（String nm , int a）构造方法中同样也有隐式的this，this的值同样也为0x001，这时会执行其中name = nm，即把“张三”赋值给成员的name。当赋值结束后Person（String nm , int a）构造方法弹栈。
	5. 程序继续执行构造方法（Person（String nm , int a）中的age = a；这时会将23赋值给成员属性age。赋值结束构造方法（Person（String nm , int a）弹栈。
	6. 当构造方法（Person（String nm , int a）弹栈结束后，Person对象在内存中创建完成，并将0x001赋值给main方法中的p引用变量。
	
### super关键字	
- 子父类中构造方法的调用
	- 在创建子类对象时，父类的构造方法会先执行，因为子类中所有构造方法的第一行有默认的隐式`super();`语句。
- 格式：
	- 调用本类中的构造方法
	`this(实参列表);`
	- 调用父类中的空参数构造方法
	`super();`
	- 调用父类中的有参数构造方法
	`super(实参列表);`

- 子类构造方法,有一个默认添加的构造方法
```Java
public class Student extends Person {
	public Student(){
		super();
	}
}
```
- 为什么子类对象创建都要访问父类中的构造方法？因为子类继承了父类的内容，所以创建对象时，必须要先看父类是如何对其内容进行初始化的
```Java
public class Test {
	public static void main(String[] args) {
		new Zi();
	}		
}
class Fu{
	int num ;
	Fu(){
		System.out.println("Fu构造方法"+num);
		num = 4;
	}
}
class Zi extends Fu{
	Zi(){
		//super(); 调用父类空参数构造方法
		System.out.println("Zi构造方法"+num);
	}
}
```
执行结果：
```shell
Fu构造方法0
Zi构造方法4
```
- 创建子类对象的时候会必须调用父类的构造方法。
	- 子类默认会调用父类的无参构造，但如果父类没有无参构造，子类的构造方法继续调用父类的无参构造就会报错。
	- 因此子类构造方法的第一行需要调用父类的构造方法，既可以调用父类的无参构造，也可以调用父类的有参构造，这样语法上就不会报错。
		
- 构造方法第一行,写this()还是super()
	- this() 是调用本类的构造方法,super()是调用父类的构造方法, 且两条语句不能同时存在
	- 保证子类的所有构造方法调用到父类的构造方法即可
	
- 小结:
	- 无论如何,子类的所有构造方法,直接或间接必须调用到父类构造方法;
	- 子类的构造方法什么都不写,默认的构造方法第一行super()

		
### 创建子类对象过程的细节
- 创建子类对象过程的细节
	* 如果子类的构造方法第一行写了this调用了本类其他构造方法，那么super调用父类的语句还有吗？
		* 这时是没有的，因为this()或者super(),只能定义在构造方法的第一行，因为初始化动作要先执行。
	* 父类构造方法中是否有隐式的super呢？
		* 只要是构造方法默认第一行都是super();
	* 父类的父类是谁呢？super调用的到底是谁的构造方法呢？
		* Java定义了一个所有对象的父类Object

- 注意：
	- 类中的构造方法默认第一行都有隐式的super()语句，在访问父类中的空参数构造方法。所以父类的构造方法既可以给自己的对象初始化，也可以给自己的子类对象初始化。
	- 如果默认的隐式super()语句在父类中没有对应的构造方法，那么必须在构造方法中通过this或者super的形式明确要调用的构造方法。