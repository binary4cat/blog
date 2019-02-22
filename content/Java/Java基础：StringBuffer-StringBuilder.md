---
title: Java基础：StringBuffer&StringBuilder
date: 2017-05-21T21:02:18+08:00
categories: ["Java"]
tags: ["Java基础"]
permalink:
description: Java基础：StringBuffer&StringBuilder
photos: http://ww1.sinaimg.cn/large/c55a7aeely1fmbwbz5s71j20et08cjra.jpg
---

# 1. StringBuffer特点可变字符数组
- `StringBuffe`r类概述
	- 线程安全的可变字符序列 
	- 底层采用字符数组实现,初始容量为16
- `StringBuffer`和`String`的区别
	- `String`是一个不可变的字符序列
	- `StringBuffer`是一个可变的字符序列
<!--more-->
# 2. StringBuffer类的方法
- StringBuffer类的方法
	- 方法介绍
		- `StringBuffer append()`, 将任意类型的数据,添加缓冲区
		- `delete(int start,int end)`: 删除缓冲区中字符
			-  开始索引包含,结尾索引不包含
		- `insert(int index, 任意类型)`: 将任意类型数据,插入到缓冲区的指定索引上
		- `replace(int start,int end, String str)`: 将指定的索引范围内的所有字符,替换成新的字符串
		- `reverse()`: 将缓冲区中的字符反转
		- `String toString()`: 继承Object,重写toString()
			-   将缓冲区中的所有字符,变成字符串
			
```Java
/*
*   StringBuffer类的方法
*   String toString() 继承Object,重写toString()
*   将缓冲区中的所有字符,变成字符串
*/
public static void StringBufferFunc(){
	StringBuffer buffer = new StringBuffer();
	buffer.append("haijd");
	buffer.append(12345);
	//将可变的字符串缓冲区对象,变成了不可变String对象
	String s = buffer.toString();
	System.out.println(s);  //输出：haijd12345
}
```


```Java				
/*
*  StringBuffer类的方法
*    reverse() 将缓冲区中的字符反转
*/
public static void StringBufferFunc(){
	StringBuffer buffer = new StringBuffer();
	buffer.append("haijd");
	buffer.reverse();
	System.out.println(buffer);  //输出：djiah
}
```


```Java				
/*
*  StringBuffer类方法
*    replace(int start,int end, String str)
*    将指定的索引范围内的所有字符,替换成新的字符串
*/
public static void StringBufferFunc(){
	StringBuffer buffer = new StringBuffer();
	buffer.append("haijd");
	buffer.replace(3, 5, "jiandong");
	System.out.println(buffer);  //输出：haijiandong
}
```


```Java				
/*
*  StringBuffer类方法 insert
*    insert(int index, 任意类型)
*  将任意类型数据,插入到缓冲区的指定索引上
*/
public static void StringBufferFunc(){
	StringBuffer buffer = new StringBuffer();
	buffer.append("haijd");	 
	buffer.insert(3, 110);
	System.out.println(buffer);  //输出：hai110jd
}
```


```Java
/*
* StringBuffer类方法
*   delete(int start,int end) 删除缓冲区中字符
*   开始索引包含,结尾索引不包含
*/
public static void StringBufferFunc(){
	StringBuffer buffer = new StringBuffer();
	buffer.append("haijd");
	buffer.delete(1,3);
	System.out.println(buffer);   //输出：hjd
}
```


```Java				
/*
*   StringBuffer类方法
*   StringBuffer append, 将任意类型的数据,添加缓冲区
*/
public static void StringBufferFunc(){
	StringBuffer buffer = new StringBuffer();
	//调用StringBuffer方法append向缓冲区追加内容
	buffer.append(6).append(false).append("hai").append(110);
	System.out.println(buffer);   //输出：6falsehai110
}
```


# 3. StringBuilder类
- `StringBuffer`和`StringBuilder`的区别
	- `StringBuffer`是jdk1.0版本的,是线程安全的,效率低
	- `StringBuilder`是jdk1.5版本的,是线程不安全的,效率高
- `String`和`StringBuffer`,`StringBuilder`的区别
	- `String`是一个不可变的字符序列
	- `StringBuffer`,`StringBuilder`是可变的字符序列
