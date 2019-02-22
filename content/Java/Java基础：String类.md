---
title: Java基础：String类
date: 2017-05-21T22:13:33+08:00
categories: ["Java"]
tags: ["Java基础"]
permalink:
description: Java基础：String类
photos: http://ww1.sinaimg.cn/large/c55a7aeely1fmbwbz5s71j20et08cjra.jpg
---

# 1. String类的概念和不变性
- String类的概念和不变性
	- String类
		- API中的String类的描述，发现String 类代表字符串
		- Java 程序中的所有字符串字面值（如 "abc" ）都作为此类的实例实现。
		- 字符串是常量,在创建之后不能更改
		- 其实就是说一旦这个字符串确定了，那么就会在内存区域中就生成了这个字符串。字符串本身不能改变，但str变量中记录的地址值是可以改变的。
		- 源码分析,String类底层采用的是字符数组:    
<!--more-->


```Java  
/*
*   String类特点:
*   一切都是对象,字符串事物 "" 也是对象
*   类是描述事物,String类,描述字符串对象的类
*   所有的 "" 都是String类的对象   
*   字符串是一个常量,一旦创建,不能改变
*/
public class StringDemo {
	public static void main(String[] args) {
		//引用变量str执行内存变化
		//定义好的字符串对象,不变
		String str = "123";
		System.out.println(str);
		str = "456";
		System.out.println(str);
	}
}
```


# 2. String类创建方式和比较
- String类创建方式和比较
	- 创建对象的数量比较
		- `String s3 = "abc";`
			- 在内存中只有一个对象。这个对象在字符串常量池中
		- `String s4 = new String("abc");`
			- 在内存中有两个对象。一个new的对象在堆中，一个字符串本身对象，在字符串常量池中

```Java
public class StringDemo2 {
	public static void main(String[] args) {
		//字符串定义方式2个, 直接“=” or 使用String类的构造方法
		String str1 = new String("abc");
		String str2 = "abc";
		System.out.println(str1);
		System.out.println(str2);
		System.out.println(str1==str2);//引用数据类型,比较对象的地址 false
		System.out.println(str1.equals(str2));//true
	}
}
```


# 3. String类构造方法
- String类构造方法
	- 常见构造方法
		- `public String()`:空构造
		- `public String(byte[] bytes)`:把字节数组转成字符串
		- `public String(byte[] bytes,int index,int length)`:把字节数组的一部分转成字符串			
		- `public String(String original)`:把字符串常量值转成字符串

```Java
public class StringDemo3 {
	public static void main(String[] args) {
		function();
	}
	/*
	*  定义方法,String类的构造方法
	*  String(byte[] bytes)  传递字节数组
	*  字节数组转成字符串
	*  通过使用平台的默认字符集解码指定的 byte 数组，构造一个新的 String。
	*  平台 : 机器操作系统
	*  默认字符集: 操作系统中的默认编码表, 默认编码表GBK
	*  将字节数组中的每个字节,查询了编码表,得到的结果
	*  字节是负数,汉字的字节编码就是负数, 默认编码表 ,一个汉字采用2个字节表示
	*  
	*  String(byte[] bytes, int offset, int length) 传递字节数组
	*  字节数组的一部分转成字符串
	*  offset 数组的起始的索引
	*  length 个数,转几个   , 不是结束的索引
	*/
	public static void function(){
		byte[] bytes = {97,98,99,100};
		//调用String类的构造方法,传递字节数组
		String s = new String(bytes);
		System.out.println(s);  //根据ASCII编码，97，98，99，100转成了字符串"abcd"
		byte[] bytes1 ={65,66,67,68,69};
		//调用String构造方法,传递数组,传递2个int值
		String s1 = new String(bytes1,1,3);
		System.out.println(s1); //根据ASCII编码，将66，67，68转成了"BCD"
	}
}
```


- 其他构造方法
	- `public String(char[] value)`:把字符数组转成字符串
	- `public String(char[] value,int index,int count)`:把字符数组的一部分转成字符串
	
```Java
public class StringDemo {
	public static void main(String[] args) {
		function();
	}
	/*
	* String(char[] value) 传递字符数组
	* 将字符数组,转成字符串, 字符数组的参数,不查询编码表
	* 
	* String(char[] value, int offset, int count) 传递字符数组
	* 将字符数组的一部分转成字符串
	* offset  数组开始索引
	* count   个数
	*/
	public static void function(){
		char[] ch = {'a','b','c','d','e','f'};
		//调用String构造方法,传递字符数组
		String s = new String(ch);
		System.out.println(s);   //直接将字符“拼”起来，输出"abcdef"
				
		String s1 = new String(ch,1,4);
		System.out.println(s1); //输出"bcde"
	}
}
```


# 4. String类的其他方法			
- String类的其他方法
	- 方法介绍
		- `int length()`: 返回字符串的长度
		- `String substring(int beginIndex,int endIndex)`: 获取字符串的一部分
		- `String substring(int beginIndex)`: 获取字符串的一部分
		- `boolean startsWith(String prefix)`: 判断一个字符串是不是另一个字符串的前缀,开头
		- `boolean endsWith(String prefix)`: 判断一个字符串是不是另一个字符串的后缀,结尾
		- `boolean contains (String s)`: 判断一个字符串中,是否包含另一个字符串
		- `int indexOf(char ch)`:  查找一个字符,在字符串中第一次出现的索引,被查找的字符不存在,返回-1
		- `byte[] getBytes()`: 将字符串转成字节数组,此功能和String构造方法相反,byte数组相关的功能,查询编码表
		- `char[] toCharArray()`: 将字符串转成字符数组,功能和构造方法相反
		- `boolean equals(Object obj)`: 方法传递字符串,判断字符串中的字符是否完全相同,如果完全相同返回true
		- `boolean equalsIgnoreCase(String s)`: 传递字符串,判断字符串中的字符是否相同,忽略大小写			
			
```Java
/*
*  boolean equals(Object obj)
*  方法传递字符串,判断字符串中的字符是否完全相同,如果完全相同返回true
*  
*  boolean equalsIgnoreCase(String s)
*  传递字符串,判断字符串中的字符是否相同,忽略大小写
*/
public static void StringFunction(){
	String str1 = "ABC";
	String str2 = "abc";
	//分别调用equals和equalsIgnoreCase
	boolean b1 = str1.equals(str2);
	boolean b2 = str1.equalsIgnoreCase(str2);
	System.out.println(b1);   //false
	System.out.println(b2);   //true
}
```


```Java				
/*
* char[] toCharArray() 将字符串转成字符数组
* 功能和构造方法相反
*/
public static void StringFunction(){
	String str = "haijd";
	//调用String类的方法toCharArray()
	char[] ch = str.toCharArray();
	for(int i = 0 ; i < ch.length ; i++){
		System.out.println(ch[i]);   //输出单个字符
		}
	}
```


```Java				
/*
*  byte[] getBytes() 将字符串转成字节数组
*  此功能和String构造方法相反
*  byte数组相关的功能,查询编码表
*/
public static void StringFunction(){
	String str = "123456";
	//调用String类方法getBytes字符串转成字节数组
	byte[] bytes = str.getBytes();
	for(int i = 0 ; i < bytes.length ; i++){
		System.out.println(bytes[i]);
	}
	//输出：49，50，51，52，53，54
}
```


```Java				
/*
*  int indexOf(char ch)
*  查找一个字符,在字符串中第一次出现的索引
*  被查找的字符不存在,返回-1
*/
public static void StringFunction(){
	String str = "haijd";
	//调用String类的方法indexOf
	int index = str.indexOf('z');
	System.out.println(index);   //输出:-1
}
```


```Java				
/*
*  boolean contains (String s)
*  判断一个字符串中,是否包含另一个字符串
*/
public static void StringFunction(){
	String str = "haijd";
	//调用String类的方法contains
	boolean b =str.contains("ai");
	System.out.println(b); //输出true
}
```


```Java				
/*
* boolean endsWith(String prefix)
* 判断一个字符串是不是另一个字符串的后缀,结尾
*/
public static void StringFunction(){
	String str = "haijd";
	//调用String类方法endsWith
	boolean b = str.endsWith("jd");
	System.out.println(b);  //输出：true
}
```


```Java				
/*
* boolean startsWith(String prefix)  
* 判断一个字符串是不是另一个字符串的前缀,开头
* howareyou
* hOw
*/
public static void StringFunction(){
	String str = "haijd";
	//调用String类的方法startsWith
	boolean b = str.startsWith("Hai");
	System.out.println(b);  //输出：false
}
```


```Java				
/*
*  String substring(int beginIndex,int endIndex) 获取字符串的一部分
*  返回新的字符串
*  包含头,不包含尾巴
*  
*  String substring(int beginIndex)获取字符串的一部分
*  包含头,后面的字符全要
*/
public static void StringFunction(){
	String str = "haijd";
	//调用String类方法substring获取字符串一部分
	str= str.substring(1, 3);
	System.out.println(str);   //输出：ai
	String str2 = "haijd";
	str2 = str2.substring(2);
	System.out.println(str2);  //输出：ijd
}
```


```Java				
/*
*  int length() 返回字符串的长度
*  包含多少个字符
*/
public static void StringFunction(){
	String str = "haijd";
	//调用String类方法length,获取字符串长度
	int length = str.length();
	System.out.println(length);  //输出：5
}
```
