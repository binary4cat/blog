---
title: string类常用函数和属性
date: 2016-02-03T01:25:40+08:00
tags: ["c#基础知识"]
categories: ["cSharp"]
permalink: String-class-functions-and-properties
description: string类常用函数和属性
---
# 1. 字符串的特殊性
　　1、字符串的不可变性，当我们在给字符串进行重新赋值、拼接、累加的过程中，都会在内存的堆空间中重新开辟一块新的空间。
　　2、我们可以将字符串看做是一个`char`类型的数组。

# 2. 字符串常用函数：

`ToCharArray();` 将字符串转换成一个char类型的数组： <!--more-->
　　使用string.ToCharArray();这个函数，就能把字符串转换成一个char类型的数组，字符串里面的每一个字符都是这个char类型数组的每一个元素。

`ToString();` 可以将所有类型的数据转换为字符串类型：
　　但是我们将一个类的对象进行ToString();的时候，给我们返回的就是这个对象所在类的命名空间。

`new string(charp[] chs);` 可以将一个char类型的数组转换为字符串：
　　首先必须在前面声明一个string类型的变量接收转换后的结果；在这里相当于创建了一个string的对象，然后把char类型的参数传进去，通过调用string对象的构造函数，帮我们完成了转换。

`ToUpper();` 将字符串转换成大写：
　　string.ToUpper(); 将调用字符串中的字母转换成大写形式并返回。

`ToLower();` 将字符串转换成小写：
　　string.ToLower(); 将调用字符串中的字母转换成小写形式并返回。

`Equals("string",StringComparison.OrdinallgnoreCase);` 可以与参数指定的字符串比较是否相同，第二个参数可以实现字符串的大小写忽略与否：
　　string.Equals("要比较的字符串")返回的是一个bool类型的值，我们一般用if去判断它。

`Split(char[],StringSplitoptions.RemoveEmptyEntries);` 可以对数组进行分割，取出不想要的部分：
　　string.Split(...)，在char类型的数组里面写上不需要的字符，然后最后跟上的是，不需要分割后出现空字符的函数。

`Contains(string);` 判断字符串中是否包含指定的字符串：
　　string.Contains("要判断的字符串"),返回一个bool类型的值。

`Replace(old_string,new_string);` 将字符串中的某些字符替换成指定的字符：
　　string.Replace("需要替换的字符或字符串","新的字符或字符串"),一般跟Contains();这个函数一起用，写在if判断里面，表示当某字符串有某些元素的时候，就替换成其他字符串。　

`SubString(int);` 截取字符串，指定开始截取的下标和截取的个数：
　　返回的是一个字符串类型的值，我们在括号里面可以写从第几个开始截取，也可以写从第几个开始截取，需要截取几个字符。SubString(2,5);   表示从该字符串的下标为2的元素开始，总共截取5个字符，如果不写截取几个，那么就一直截取到最后。

`StartWith(string);` 是否以指定的字符串开头，返回一个bool类型的值：
　　string.StartWith("开始字符串");判断一个字符串是否以指定的字符或者字符串作为开头。

`EndsWith(string);` 是否以指定的字符串结尾，返回一个bool类型的值：
　　string.EndsWith("结尾字符串");判断一个字符串是否以指定的字符或者字符串作为结尾。

`IndexOf(string);` 查找指定的字符或者字符串在调用字符串中第一次出现的位置(下标)，返回int类型的值：
　　string.IndexOf("第一次出现的字符串或字符");拿到一个字符串或字符在字符串中第一次出现的位置(下标)。

`LastIndexOf(string);` 查找指定的字符串或者字符在调用字符串中最后一次出现的位置(下标)，返回int类型的值：
　　string.LastIndexOf("最后一次出现的字符串或字符");拿到一个字符串或字符在字符串中最后一次出现的位置(下标)。

`Trim();` 去掉字符串两端的空格：
　　string.Trim();去掉调用字符串两端的空格，中间的去不掉。

`TrimStart();` 去掉字符串左边(开头)的空格：
　　string.TrimStart();去掉字符串开头的空格，中间和结尾的空格去不掉。

`TrimEnd();` 去掉字符串右边(结尾)的空格：
　　string.TrimEnd();去掉字符串结尾的空格，中间和开头的空格去不掉。

# 3. 两个静态函数

`IsNullOrEmpty();` 判断指定字符串是否为null或者为空，返回一个bool类型的值：
　　string.IsNullOrEmpty();判断指定字符串是否为空或者为null，返回一个bool类型的值。

`Join(string);` 将指定的字符插入字符串的每一个元素的结尾，最后一个元素后面不加：
　　string.Join("要插入的字符");将指定的字符插入调用字符串的每一个元素的后面，最后一个元素默认不加，返回的是string类型的值。

# 4. 字符串常用属性

`Length` 返回字符串的长度：
　　string.Length;可以拿到调用字符串的长度(字符个数)，返回一个int类型的值。
