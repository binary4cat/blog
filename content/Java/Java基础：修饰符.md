---
title: Java基础：修饰符
date: 2017-05-11T13:04:09+08:00
categories: ["Java"]
tags: ["Java基础"]
permalink:
description: Java基础：修饰符
photos: http://ww1.sinaimg.cn/large/c55a7aeely1fmbwbz5s71j20et08cjra.jpg
---
<!-- TOC -->

- [权限修饰符](#权限修饰符)
- [常用来修饰类、方法、变量的修饰符如下：](#常用来修饰类方法变量的修饰符如下)
- [不能同时使用的修饰符](#不能同时使用的修饰符)
    - [修饰类能够使用的修饰符：](#修饰类能够使用的修饰符)
- [修饰成员变量能够使用的修饰符：](#修饰成员变量能够使用的修饰符)
- [修饰构造方法能够使用的修饰符：](#修饰构造方法能够使用的修饰符)
- [修饰成员方法能够使用的修饰符：](#修饰成员方法能够使用的修饰符)

<!-- /TOC -->
### 权限修饰符
- 权限修饰符有哪些
	- 在Java中提供了四种访问权限，使用不同的访问权限时，被修饰的内容会有不同的访问权限  

| 范围                   | public | protected | default | private |
| :--------------------- | :----- | :-------- | :------ | :------ |
| 同一类中               | ✔      | ✔         | ✔       | ✔       |
| 同一包中(子类与无关类) | ✔      | ✔         | ✔       |         |
| 不同包的子类           | ✔      | ✔         |         |         |
| 不同包中的无关类       | ✔      |           |         |         |
<!--more-->
- 要想仅能在本类中访问使用`private`修饰；
- 要想本包中的类都可以访问不加修饰符即可；
- 要想本包中的类与其他包中的子类可以访问使用`protected`修饰
- 要想所有包中的所有类都可以访问使用`public`修饰。
- 如果类用public修饰，则类名必须与文件名相同。一个文件中只能有一个`public`修饰的类。
	
### 常用来修饰类、方法、变量的修饰符如下：
- `public` 权限修饰符，公共访问, 类,方法,成员变量
- `protected` 权限修饰符，受保护访问, 方法,成员变量
- 默认什么也不写 也是一种权限修饰符，默认访问, 类,方法,成员变量
- `private` 权限修饰符，私有访问, 方法,成员变量
- `static` 静态修饰符  方法,成员变量
- `final` 最终修饰符   类,方法,成员变量,局部变量
- `abstract` 抽象修饰符  类 ,方法

### 不能同时使用的修饰符
- `abstract`与`private`不能同时使用；
- `abstract`与`static`不能同时使用；
- `abstract`与`final`不能同时使用。

#### 修饰类能够使用的修饰符：
- 修饰类只能使用`public`、默认的、`final`、`abstract`关键字
- 使用最多的是`public`关键字

```Java
public class Test {} //最常用的方式
class Test2{}
public final class Test3{}
public abstract class Test4{}
```


### 修饰成员变量能够使用的修饰符：
- `public` : 公共的
- `protected` : 受保护的
- 	: 默认的
- `private` ：私有的
- `final` : 最终的
- `static` : 静态的
- 使用最多的是`private`

```Java
public int i = 100;
protected int i2 = 100;
int i3 = 100;
private int i4 = 100; //最常用的方式
public final int i5 = 100;
public static int i6 = 100;
```


### 修饰构造方法能够使用的修饰符：
- `public` : 公共的
- `protected` : 受保护的
- 	: 默认的
- `private` ：私有的
- 使用最多的是`public`

```Java
public Test(){} //最常用的方式
protected Test(){}
Test(){}
private Test(){}
```


### 修饰成员方法能够使用的修饰符：
- `public` : 公共的
- `protected` : 受保护的
- : 默认的
- `private` ：私有的
- `final` : 最终的
- `static` : 静态的
- `abstract` : 抽象的
- 使用最多的是`public`

```Java
public void method1(){}//最常用的方式
protected void method2(){}
void method3(){}
private void method4(){}
public final void method5(){}
public static void method6(){}//最常用的方式
public abstract void method7();//最常用的方式
```

