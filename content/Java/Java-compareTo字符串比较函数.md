---
title: Java compareTo字符串比较函数
date: 2017-12-10T19:23:27+08:00
categories: ["Java"]
tags: ["Java基础"]
permalink: Java-compareTo-String-Function
description: compareTo字符串比较函数
photos: 
- http://ww1.sinaimg.cn/large/c55a7aeely1fmbwbz5s71j20et08cjra.jpg
---
<!-- TOC -->

- [1. 调用方式：](#1-%E8%B0%83%E7%94%A8%E6%96%B9%E5%BC%8F%EF%BC%9A)
- [2. 关于返回值的三种判断](#2-%E5%85%B3%E4%BA%8E%E8%BF%94%E5%9B%9E%E5%80%BC%E7%9A%84%E4%B8%89%E7%A7%8D%E5%88%A4%E6%96%AD)

<!-- /TOC -->
# 1. 调用方式：

`int compareTo(String)`
compareTo函数可以直接使用String类型的变量调用，参数是需要比较的另一个字符串，返回值是一个int类型。

# 2. 关于返回值的三种判断

`s1.compareTo(s2)//s1是原字符串，s2是参数字符串`
返回值计算方式：*原减参*

- 返回`0`
  - 两个字符串相同
- 返回正负整数：
  - 原字符串中的所有字符，按照顺序都包含在参数字符串中时，返回的是，原字符串字符数减去参数字符串字符数。

    ```java
    String s1 = "Java";
    String s2 = "JavaScript";
    System.Out.Println(s1.compareTo(s2));  //输出-6
    //解读：因为s1是原字符串，并且其中的4个字符全部在参数字符串s2中可以找到，
    // 所以在使用compareTo函数的时候，就是源字符串的长度4减去参数字符串的长度10等于-6
    ```

  - 原字符串中有1个或以上的字符并不在参数字符串中时，返回的是，两个字符串中不相同的第一个字符的ASCII码(十进制表示)相减的值。

    ```Java
    String s1 = "Javac";  
    String s2 = "JavaScript";
    System.Out.Println(s1.compareTo(s2));  //输出16
    // 解读：字符串s1中的第五个字符“c”对应的是s2中的“S”，
    // 所以两个是不包含的关系，使用ASCII码的十进制表示相减。
    // “c”的ASCII码为99，“S”的ASCII码为83，利用“原减参”的规则，99-83=16
    ```

> 使用人话说：
我找你想比较一下我们俩拥有的水果是不是一模一样的，我有4个苹果，你也有4个苹果，但是你还有3个梨，这时候，我只能表示我比你少3个数量的水果。
过了一会儿，我从家里拿来一个橘子，加在一起和你比较了，这时候，橘子是只有我有，而你没有，这时候我才不管你有梨我没梨了，我就非得用这个橘子和你的梨比较价格。
