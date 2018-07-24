---
title: Java基础：Data&Calendar
date: 2017-05-25T19:18:10+08:00
categories: ["Java"]
tags: ["Java基础"]
permalink:
description: Java基础：Data&Calendar
photos: http://ww1.sinaimg.cn/large/c55a7aeely1fmdrn8b8i8j20go0dejrs.jpg
---
<!-- TOC -->

- [Date类的构造方法](#date%E7%B1%BB%E7%9A%84%E6%9E%84%E9%80%A0%E6%96%B9%E6%B3%95)
- [Date类的get和set方法](#date%E7%B1%BB%E7%9A%84get%E5%92%8Cset%E6%96%B9%E6%B3%95)
- [日期格式化SimpleDateFormat](#%E6%97%A5%E6%9C%9F%E6%A0%BC%E5%BC%8F%E5%8C%96simpledateformat)
- [字符串转成日期对象](#%E5%AD%97%E7%AC%A6%E4%B8%B2%E8%BD%AC%E6%88%90%E6%97%A5%E6%9C%9F%E5%AF%B9%E8%B1%A1)
- [Calendar类](#calendar%E7%B1%BB)
- [set(int field,int value)和add(int field, int value)的区别](#setint-fieldint-value%E5%92%8Caddint-field-int-value%E7%9A%84%E5%8C%BA%E5%88%AB)

<!-- /TOC -->
### Date类的构造方法
- Date类的构造方法
	- 空参构造
		- `public Date()`
	- 带参构造
		- `public Date(long times)`
	
### Date类的get和set方法			
- Date类的get和set方法
	- `public long getTime()`	
		- 将当前的日期对象，转为对应的毫秒值
	- `public void setTime(long times);`
		- 根据给定的毫秒值，生成对应的日期对象

<!--more-->
### 日期格式化SimpleDateFormat
- 日期格式化SimpleDateFormat
   - SimpleDateFormat函数的继承关系：
    java.lang.Object
        |
        +----java.text.Format
           |
           +----java.text.DateFormat
                   |
                   +----java.text.SimpleDateFormat
	- 对日期进行格式化(自定义)
		- 对日期格式化的类 `java.text.DateFormat` 抽象类, 普通方法,也有抽象的方法
		- 实际使用是子类 `java.text.SimpleDateFormat` 可以使用父类普通方法,重写了抽象方法
	- 对日期进行格式化的步骤
		- 创建`SimpleDateFormat`对象
			- 在类构造方法中,写入字符串的日期格式 (自己定义)
		- `SimpleDateFormat`调用方法format对日期进行格式化
			- `public String format(Date date)` 传递日期对象,返回字符串
			- 日期模式:
	 		    - yyyy    年份
	 		    - MM      月份
			    - dd      月中的天数
			    - HH       0-23小时
			    - mm      小时中的分钟
	 		    - ss      秒
	 		    - yyyy年MM月dd日 HH点mm分钟ss秒
```Java
SimpleDateFormat myFmt=new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒");
SimpleDateFormat myFmt1=new SimpleDateFormat("yy/MM/dd HH:mm"); 
Date now=new Date();
System.out.println(myFmt.format(now));  //输出：2017年05月25日 18时17分35秒
System.out.println(myFmt1.format(now)); //输出：17/05/25 18:17
```

			
### 字符串转成日期对象
- 字符串转成日期对象
	- 使用步骤
		- 创建SimpleDateFormat的对象
			- 构造方法中,指定日期模式
		- 子类对象,调用方法 parse 传递String,返回Date
			- 注意: 时间和日期的模式yyyy-MM-dd, 必须和字符串中的时间日期匹配
```Java
SimpleDateFormat myFmt=new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒");
String now = "2017年05月25日 18时17分35秒";
System.out.println(myFmt.parse(now));  //输出：Thu May 25 18:17:35 CST 2017
```


### Calendar类
- 日历类(抽象类)
	- 所在包：`java.util.Calendar`
- 创建对象
	- `Calendar`类写了静态方法 `getInstance()` 直接返回了子类的对象
	- 不需要直接new子类的对象,通过静态方法直接获取
- 成员方法
	- `getTime()` 把日历对象,转成Date日期对象
	- `get(int field)` 获取指定日期类型的值
```Java
Calendar c = Calendar.getInstance();
// 获取年份
int year = c.get(Calendar.YEAR);
// 获取月份
int month = c.get(Calendar.MONTH) + 1;
// 获取天数
int day = c.get(Calendar.DAY_OF_MONTH);
System.out.println(year + "年" + month + "月" + day + "日");
```


- 成员方法
	 `set(int field,int value)`  给指定的日期类型设置时间 (field代表了Calendar定义的一些字段，例如:Calendar.YEAR=1，再这里写1和Calendar.YEAR都可以，都是int常量数值，但是推荐使用Calendar.YEAR这种形式，因为比较容易识别，也不容易写错)
     `set(int year, int month, int date)`  设置指定的时间
```Java
public static void CalendarFunc() {
	Calendar c = Calendar.getInstance();
	// 设置,月份,设置到10月分
	c.set(Calendar.MONTH, 9);
	// 设置年,月,日
	c.set(2017, 5, 25);
	// 获取年份
	int year = c.get(Calendar.YEAR);
	// 获取月份
	int month = c.get(Calendar.MONTH);
	// 获取天数
	int day = c.get(Calendar.DAY_OF_MONTH);
	System.out.println(year + "年" + month + "月" + day + "日");
}
```


- 成员方法
	- `add(int field, int value)` 进行整数的偏移(给当前日期类型增加或减少)
```Java
public static void function_2() {
	Calendar c = Calendar.getInstance();
	// 让日历中的天数,向后移90天
	c.add(Calendar.DAY_OF_MONTH, -90);
	// 获取年份
	int year = c.get(Calendar.YEAR);
	// 获取月份
	int month = c.get(Calendar.MONTH) + 1;
	// 获取天数
	int day = c.get(Calendar.DAY_OF_MONTH);
	System.out.println(year + "年" + month + "月" + day + "日");  //输出：2017年3月27日
}
```


### set(int field,int value)和add(int field, int value)的区别
- add：调整时间，在当前的时间的基础上增加或者减少，超过最大值，会全部增加。
- set：设置时间，直接将指定的日期类型设置成指定的值，超过类型的最大值，会自动计算，例如set(Calendar.MONTH,13)，那么就会折合成1年零一个月，年份增加1年，月份为折合后的余数。
```Java
Calendar c = Calendar.getInstance();
c.set(2017,5, 25);
c.set(Calendar.MONTH,1);    //获得：2017-1-25
c.set(Calendar.MONTH,13);   //获得：2018-1-25

c.add(Calendar.MONTH, 1);   //获得：2017-6-25
c.add(Calendar.MONTH, 13);  //获得：2018-6-25
```
