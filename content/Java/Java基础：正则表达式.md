---
title: Java基础：正则表达式
date: 2017-05-22T20:10:45+08:00
categories: ["Java"]
tags: ["Java基础"]
permalink:
description: Java基础：正则表达式
photos: http://ww1.sinaimg.cn/large/c55a7aeely1fmdrn8b8i8j20go0dejrs.jpg
---

# 1. 正则表达式的概念和作用
- 正则表达式的概述
	- 正则表达式也是一个字符串，用来定义匹配规则，在Pattern类中有简单的规则定义。可以结合字符串类的方法使用。
	- 正则表达式是具有特殊含义的字符串。
- 正则表达式的作用
	- 比如注册账户,需要输入用户名和密码,一般会对其限制长度,这个限制长度的事情就是正则表达式做的
<!--more-->		
# 2. 正则表达式语法规则
- 正则表达式语法规则
	- 字符
		- `x`  代表的是字符x
		- `\\` 代表的是反斜线字符'\'
		- `\t` 代表的是制表符
		- `\n` 代表的是换行符
		- `\r` 代表的是回车符
	- 字符类
		- `[abc]`    a、b 或 c（简单类）
		- `[^abc]`   任何字符，除了 a、b 或 c（否定）
		- `[a-zA-Z]` a到 z 或 A到 Z，两头的字母包括在内（范围） 
		- `[0-9]`    0到9的字符都包括
		- `[a-zA-Z_0-9]` 代表的字母或者数字或者下划线
	- 预定义字符类
		- `.` 任何字符。
		- `\d` 数字：[0-9]
		- `\w` 单词字符：[a-zA-Z_0-9]如"com.itheima.tests"/finish
	- 边界匹配器
		- `^`  代表的是行的开头
		- `$`  代表的是行的结尾
		- `\b` 代表的是单词边界
	- 数量词
		- `X?`     X，一次或一次也没有
		- `X*`     X，零次或多次
		- `X+`     X，一次或多次
		- `X{n}`   X，恰好 n 次 
		- `X{n,}`  X，至少 n 次 
		- `X{n,m}` X，至少 n 次，但是不超过 m 次


# 3. 正则表达式相关的String类方法
- 正则表达式练习和相关的String类方法
	- `boolean matches(String 正则的规则)`
		- `"abc".matches("[a]")` 
		- 匹配成功返回true
	- `String[] split(String 正则的规则)`
		- `"abc".split("a")` 
		- 使用规则将字符串进行切割
	- `String replaceAll( String 正则规则,String 字符串)`
		- `"abc0123".repalceAll("[\\d]","#")`	
		- 按照正则的规则,替换字符串
	
	
# 4. 匹配示例

```Java
/*
*  检查手机号码是否合法
*  1开头 第二位可以是34578 位数固定11位
*/
public static void checkPhone(){
	String telNumber = "158123456789";
	//String类的方法matches
	boolean b = telNumber.matches("1[34857][\\d]{9}");
	System.out.println(b);   //输出：true
}
				
/*
*  检查QQ号码是否合法
*  0不能开头,全数字, 位数5,10位
*  123456 
*  \\d  \\D匹配不是数字
*/
public static void checkQQ(){
	String QQ = "123456";
	//检查QQ号码和规则是否匹配,String类的方法matches
	boolean b = QQ.matches("[1-9][\\d]{4,9}");
	System.out.println(b); //输出：true
}
```


# 5. 切割示例

```Java
/*
* String类方法split对字符串进行切割
* www.baidu.com 按照 . 切割字符串
*/
public static void splitExample(){
	String ip = "www.baidu.com";
	String[] strArr = ip.split("\\.");
	for(int i = 0 ; i < strArr.length ; i++){
		System.out.println(strArr[i]);   //分别输出：www  baidu  com
	}
}
```


```Java				
/*
* String类方法split对字符串进行切割
* 按照空格切割字符串
*/
public static void splitExample(){
	String str = "h ai j     d";
	String[] strArr = str.split(" +");
	for(int i = 0 ; i < strArr.length ; i++){
		System.out.println(strArr[i]);  //分别输出：h  ai  j  d
	}
}
```


```Java				
/*
*  String类方法split对字符串进行切割
*  按照-对字符串进行切割
*/
public static void splitExample(){
    String str = "hai-j-d";
	//按照-对字符串进行切割,String类方法split
	String[] strArr = str.split("-");
	for(int i = 0 ; i < strArr.length ; i++){
		System.out.println(strArr[i]);  //分别输出：hai j d
	}
}	
```


# 6. 替换示例

```Java
/*
* 将字符串中出现的所有数字替换掉
* String类方法replaceAll(正则规则,替换的内容)
*/
public static void replaceExample(){
	String str = "hai123";
	str = str.replaceAll("[\\d]+", "jd");
	System.out.println(str);  //输出haijd
}
```
