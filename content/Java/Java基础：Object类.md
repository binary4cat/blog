---
title: Java基础：Object类
date: 2017-05-12T21:56:17+08:00
categories: ["Java"]
tags: ["Java基础"]
permalink:
description: Java基础：Object类
photos: http://ww1.sinaimg.cn/large/c55a7aeely1fmbwbz5s71j20et08cjra.jpg
---
<!-- TOC -->

- [Object类概述](#object%E7%B1%BB%E6%A6%82%E8%BF%B0)
- [equals方法比较内存地址](#equals%E6%96%B9%E6%B3%95%E6%AF%94%E8%BE%83%E5%86%85%E5%AD%98%E5%9C%B0%E5%9D%80)
- [重写equals方法](#%E9%87%8D%E5%86%99equals%E6%96%B9%E6%B3%95)
- [重写toString方法](#%E9%87%8D%E5%86%99tostring%E6%96%B9%E6%B3%95)

<!-- /TOC -->
### Object类概述
- Object类概述
	- 类层次结构的根类
	- 所有类都直接或者间接的继承自该类
	- Object中描述的所有方法子类都可以使用
	- 所有类在创建对象的时候，最终找的父类就是Object。
			
### equals方法比较内存地址
- Object类中的equals方法
	- 用于比较两个对象是否相同，Object类中就是使用两个对象的内存地址在比较。
	- Object类中的`equals`方法内部使用的就是`==`比较运算符。
<!--more-->

```Java
public class Person { //默认继承Object类
	private String name;
	private int age;
	public Person(){}
    public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}
	/*
	* 重写父类的方法
	*/
	public boolean equals(Object obj){					
		return this == obj;
	}		
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}				 
}
//测试代码
public class TestEquals {
	public static void main(String[] args) {
		Object p = new Person("zhangsan",11);
		Person p2 = new Person("lisi",12);
		System.out.println(p.equals(p2));   //输出：false
	}
}

```


### 重写equals方法
- 重写equals方法
	- 开发中要比较两个对象是否相同，经常会根据对象中的属性值进行比较	
	- 在开发经常需要子类重写equals方法根据对象的属性值进行比较。	
	- `==`号和`equals`方法的区别
		- `==`是一个比较运算符号，既可以比较基本数据类型,也可以比较引用数据类型，基本数据类型比较的是值,引用数据类型比较的是地址值
		- `equals`方法是一个方法，只能比较引用数据类型，所有的对象都会继承`Object`类中的方法，如果没有重写`Object`类中的`equals`方法， `equals`方法和`==`号比较引用数据类型无区别。

```Java
public class Person extends Object{
	private String name;
	private int age;
	public Person(){}
	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}
	//重写父类的方法toString()
	public String toString(){
		return name + age;
	}
	//重写equals方法，比较两个对象的属性是否相同
	public boolean equals(Object obj){
		if( this == obj){
			return true;
		}
		//对参数obj,非null判断
		if( obj == null){
			return false;
		}
		if( obj instanceof Person){
			// 参数obj接受到是Person对象,才能转型
			// 对obj参数进行类型的向下转型,obj转成Person类型
			Person p = (Person)obj;
			return this.age ==  p.age;
		}
		return false;
	}				
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}				 
}
//测试代码
public class TestEquals {
	public static void main(String[] args) {
		Person p = new Person("zhangsan",20);
		Person p2 = new Person("lisi",20);
		System.out.println(p.equals(p2));  //输出：true
	}
}			

```


### 重写toString方法
- 重写toString方法
	- 为什么要重写toString方法
		- toString方法返回该对象的字符串表示，其实该字符串内容就是对象的类型+@+内存地址值。
		- 由于toString方法返回的结果是内存地址，而在开发中，经常需要按照对象的属性得到相应的字符串表现形式，因此也需要重写它。
		- Object类中的toString的核心代码
        
		```Java
		getClass().getName() + "@" + Integer.toHexString(hashCode()) 
		```
        - 由于默认情况下的数据对我们来说没有意义，一般建议重写该方法。

```Java
public String toString(){
	return name + age;
}	
//测试代码
public class TestToString {
	public static void main(String[] args) {
		//调用Person类的方法toString()
		//输出语句中,写的是一个对象,默认调用对象的toString方法
		Person p = new Person("zhangsan",20);
		String s = p.toString();
        //输出对象的时候，默认会调用类中的toString方法，没有重写的时候，调用的是Object默认的toString方法，打印的是对象地址
		System.out.println(p);   //输出：zhangsan20
		System.out.println(s);   //输出：zhangsan20
	}
}

```

