---
title: 5分钟概览TypeScript
date: 2018-04-15T20:03:07+08:00
categories: ["Frontend"]
tags: ["TypeScript"]
permalink: 5-minutes-to-fit-the-TypeScript
description: 5分钟快速概览上手TypeScript
photos: https://lc-mhke0kuv.cn-n1.lcfile.com/4efd9a4018b57efdcf77.png
---
<!-- TOC -->

- [1. 简介](#1-简介)
- [2. 语法特性](#2-语法特性)
- [3. 基础语法](#3-基础语法)
    - [3.1. 声明语法:](#31-声明语法)
    - [3.2. 基础类型：](#32-基础类型)
    - [3.3. 属性和函数](#33-属性和函数)

<!-- /TOC -->

> 5分钟快速上手，请猛击[这里](https://www.tslang.cn/docs/handbook/typescript-in-5-minutes.html)看官网文档。  
  
# 1. 简介  

　　TypeScript不是一种新的语言，可以认为是给JavaScript添加语言特性的语言扩展(例如在JavaScript还未出现import和装饰器的时候，TypeScript已经有了)，官方的说法是JavaScript的超集，最终还是会编译成JavaScript代码在浏览器执行。

# 2. 语法特性

　　TypeScript是支持OOP开发的，对于有OOP经验的人来说是非常友好的，写过c#、Java、golang语言的开发者，很容易上手。  
　　学习的时候，建议通读文档，中文文档非常全面，更新的也特别及时，建议多做参考，文档地址：[点此](https://www.tslang.cn/docs/home.html)！

# 3. 基础语法

## 3.1. 声明语法:

- 类、接口声明：

```typescript
class Name{
    // 声明成员
}
interface IName{
    // 声明成员
}
```

- 变量声明:  

```typescript
let num: number = 1;  // 显示赋值
let str: string;      // 默认初始值
```

- 函数声明：

```typescript
function func(val: string):void{
    // 该函数需要一个string类型的参数，无返回值。
}
```

## 3.2. 基础类型：

TypeScript中的类型和JavaScript中某些是一致的，有：

- 布尔类型：`boolean`
- 数字类型：`number`
- 字符串类型：`string`
- 数组：`number[]`或者泛型方式`Array<T>`
- 元组(Tuple)：`[string,number]`
    - 元组也是一个数组，不同的是，元组是已知长度和类型的数组：

    ```typescript
    let tup: [string,number];
    tup = ["str",123];    // 正确
    tup = [123,"str"];    // 错误
    ```
    
- 枚举(enum):

```typescript
enum Color{
    Red = 1,
    Blue,
    Black
}
```

- any：表示任意类型，不会在编译阶段对Any类型的变量进行类型检查，相当于c#中的`dynamic`。

```typescript
let notType: any = 123;
notType = "123";
notType = false;
```

- void：这和any类型相反，表示没有任何类型，通常用在函数没有返回值的情况下，因为声明一个void类型没有什么用，void类型只能赋值undefined和null。

```typescript
let nothing: void = undefined;
```

## 3.3. 属性和函数

- 属性和函数都可以用public、protected和private修饰，默认的类、接口以及属性和函数都是public的，想要私有成员，需要显示的声明private；protected表示可以在当前类和它的子类中访问的成员。
- 属性可以声明只读属性：`private readonly _num: number = 123;`
- 属性和函数都可以声明成静态属性/函数；包含静态成员的类，不需要new就可以调用其静态成员。静态成员中this关键字不指向声明它的类。
- 属性具有存取器`getter`和`setter`，可以是实现对属性的访问控制：

```typescript
class Person{
    private _name: string;
    public get name():string{
        return this._name;
    }
    public set name(val: string){
        this._name = val;
    }
}
```

- 函数的参数中可以使用可选参数，表示调用该方法时，可以不传入该参数：

```typescript
class Person{
    function getName(age: number, gender?: string){

    }
    function getName1(age: number,gender: string){

    }
}
let p :Person = new Person();
p.getName(23);  // 正确
p.getName1(23,"girl");  // 正确
p.getName1(23)  // 错误
```

- 函数参数可以设定默认值：

```typescript
class Person{
    function getName(age: number, gender = "girl"){
        return age + "***" + gender;
    }
}
let p :Person = new Person();
p.getName(23);  // 23***girl
p.getName(23, undefined); // 23***girl
p.getName(23, "boy");   // 23***boy
```

TypeScript的特性还有很多，但是了解上面的简单特性基本上就能书写TypeScript代码了(可能对于没有接触过后端的开发者还是有点绕)，还是一句话，通读官方文档，那是非常好的入门和提升的资料。
