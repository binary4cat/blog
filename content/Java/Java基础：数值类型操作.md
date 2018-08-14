---
title: Java基础：数值类型操作
date: 2017-05-30T23:51:32+08:00
categories: ["Java"]
tags: ["Java基础"]
permalink:
description: Java基础：数值类型操作
photos: http://ww1.sinaimg.cn/large/c55a7aeely1fmbwbz5s71j20et08cjra.jpg
---
<!-- TOC -->

- [Math类的方法](#math类的方法)
- [BigInteger类概述和构造方法](#biginteger类概述和构造方法)
- [BigDecimal类操作高精度数据](#bigdecimal类操作高精度数据)

<!-- /TOC -->
### Math类的方法
- `public static double sqrt(double a)`
    - 返回参数的平方根  

```Java
public static void function(){
double d = Math.sqrt(7);
   	System.out.println(d);  //输出：2.6457513110645907
}
```


<!--more-->
- `static double pow(double a, double b)`
    - 返回a的b次方 
	
```Java
public static void function(){
   	double d = Math.pow(2, 3);
   	System.out.println(d); //输出：8.0
}
```


- `static double floor(double d)`
	- 返回小于或者等于参数d的最大整数(地板函数)    

```Java
public static void function(){
   	double d = Math.floor(1.5);
   	System.out.println(d);  //输出：1.0
}
```


- `static double ceil(double d)`
	- 返回大于或者等于参数d的最小整数(天花板函数)   

```Java
public static void function(){
   	double d = Math.ceil(1.1);
   	System.out.println(d);   //输出：2.0
}
```


- `static int abs(int i)`
	- 获取参数的绝对值   

```Java
public static void function(){
   	int i = Math.abs(-3);
   	System.out.println(i); //输出：3
}
```



- `static double round(doubl d)`
    - 获取参数的四舍五入,取整数    

```Java
public static void function(){
  	double d = Math.round(1.4999); 
  	System.out.println(d);  //输出：1.0
}
```



- `static double random()`
    - 返回随机数 0.0-1.0之间   

```Java
public static void function(){
  	for(int i = 0 ; i < 10 ;i++){
  		double d = Math.random();
  		System.out.println(d); //0.35730327457447275   0.9478733976632037
  	}
}
```


### BigInteger类概述和构造方法
- BigInteger类概述和构造方法
    - BigInteger类可以对超大的数值进行操作(远超long类型的最大长度)
    - 构造方法中传递的字符串参数不允许出现数字以外的字符

```Java
/*
* BigInteger类的构造方法
* 传递字符串,要求数字格式,没有长度限制
*/
public static void function(){
   	BigInteger b = new BigInteger("8465846668464684562385634168451684568645684564564");
   	System.out.println(b);
   	BigInteger b1 = new BigInteger("5861694569514568465846668464684562385634168451684568645684564564");
   	System.out.println(b1);
}
```


- BigInteger类四则运算

```Java
public static void function_1(){
	BigInteger b1 = new BigInteger("5665464516451051581613661405146");
	BigInteger b2 = new BigInteger("965855861461465516451051581613661405146");
		 
	//计算 b1+b2对象的和,调用方法 add
	BigInteger bigAdd = b1.add(b2);//965855867126930032902103163227322810292
	System.out.println(bigAdd);
		 
	//计算b1-b2对象的差,调用方法subtract
	BigInteger bigSub = b1.subtract(b2);
	System.out.println(bigSub);
		 
	//计算b1*b2对象的乘积,调用方法multiply
	BigInteger bigMul = b1.multiply(b2);
	System.out.println(bigMul);
		 
	//计算b2/b1对象商,调用方法divied
	BigInteger bigDiv = b2.divide(b1);
	System.out.println(bigDiv);
}
```


### BigDecimal类操作高精度数据
- BigDecimal类概述
    - BigDecimal可以操作超大型的浮点数据，提供高精度的浮点运算

- 反例：

```Java
/*
* 下列计算结构都不是我们想象中的精度运算
* 原因: 计算机二进制中,表示浮点数不精确造成
*/
System.out.println(0.09 + 0.01);//0.09999999999999999
System.out.println(1.0 - 0.32);//0.6799999999999999
System.out.println(1.015 * 100);//101.49999999999999
System.out.println(1.301 / 100);//0.013009999999999999

```


- BigDecimal类实现加法减法乘法

```Java
public static void function(){
  	BigDecimal b1 =  new BigDecimal("0.09");
  	BigDecimal b2 =  new BigDecimal("0.01");
  	//计算b1+b2的和,调用方法add
  	BigDecimal bigAdd = b1.add(b2);
  	System.out.println(bigAdd);  //输出：0.10
  		
  	BigDecimal b3 = new BigDecimal("1");
  	BigDecimal b4 = new BigDecimal("0.32");
  	//计算b3-b2的差,调用方法subtract
  	BigDecimal bigSub = b3.subtract(b4);
  	System.out.println(bigSub);  //输出：0.68
  		
  	BigDecimal b5 = new BigDecimal("1.015");
  	BigDecimal b6 = new BigDecimal("100");
  	//计算b5*b6的成绩,调用方法 multiply
  	BigDecimal bigMul = b5.multiply(b6);
  	System.out.println(bigMul);  //输出：101.500
}
```


- BigDecimal类实现除法
    - `divide(BigDecimal divisor, int scale, int roundingMode)`
        - int scale : 保留几位小数
        - int roundingMode : 保留模式
            - `static int ROUND_UP` 向上+1
            - `static int ROUND_DOWN` 直接舍去
            - `static int ROUND_HALF_UP` >= 0.5 向上+1
            - `static int ROUND_HALF_DOWN` > 0.5 向上+1 ,否则直接舍去
			
```Java
public static void function_1(){
	BigDecimal b1 = new BigDecimal("1.1314");
	BigDecimal b2 = new BigDecimal("100");
	//计算b1/b2的商,调用方法divied
	BigDecimal bigDiv = b1.divide(b2,6,BigDecimal.ROUND_HALF_UP); 
	System.out.println(bigDiv);  //输出：0.011314
}
```
