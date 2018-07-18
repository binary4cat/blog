title: angular.module()的简单使用
date: 2015-07-13T23:17:26+08:00
tags: AngularJS
categories: Frontend
permalink: Angular-module-simple-to-use
description: angular.module()的简单使用
---
　　今天记录一下angularJS的核心入口ng-module。


```
var myApp=angular.module('app',[]); //创建ng-app的模型
```
　　这一句是我们写AngularJS代码的入口，也是基础知识。整个文件或者项目的运行就是基于这个方法得以继续的。在AngularJS的源代码中`module`方法是作为一个方法函数赋值给了`angular`这个全局对象的，所以有`angular`这个顶层对象直接调用。<!--more-->
　　作为整个AngularJS程序的入口，`module`只能有一个，准确的来说应该是在html代码中只能有一个`ng-app`指令(这并不是绝对的，在开发中我们也可以借用`bootstrap`这个工具方法去加载其他的`ng-app`指令，但是这样做可能会造成混乱，暂时不讨论这个问题，默认一个程序只有一个`ng-app`指令)。
　　`module`方法需要两个参数，一个是`ng-app`的参数名称，第二个参数是程序AngularJS程序需要的一些依赖项(如果没有依赖就写空数组)。
　　在大多数编码中，我们需要将程序的不同功能拆分开来，形成细分，让结构更明确，代码易于维护。这是我们会考虑如何去创建这些功能，假如我们需要使用`controller`和`service`的话，我们必须在`angulr.module`的基础上去调用他们的方法，我们如何在另一个拆分的功能中拿到这个唯一的`angular.module`呢？

如果代码像是这样：
```
var myApp1=angular.module('app',[]);
myApp1.controller(...);
myApp1.factory(...);

var myApp2=angular.module('app',[]);
myApp2.controller(...);
myApp2.factory(...);
```
　　这显然是错误的方法，排除掉使用`bootstrap`强制注册的方式，我们已经注册过一个`app`的模型，继续注册肯定是行不通的。

正确的代码是：
```
var myApp1=angular.module('app',[]);
myApp1.controller(...);
myApp1.factory(...);

var myApp2=angular.module('app');
myApp2.controller(...);
myApp2.factory(...);
```
　　这段代码跟之前的区别就是`module`方法没有了第二个参数，把传递依赖的数组去掉了。这样就代表了调用，我们调用了之前`app`的模型，而不是创建一个新的模型。
　　所以当需要拆分一个AngularJS代码时，给`module`方法传入`ng-app`的名称这一个参数，实现调用已经注册的模型。

　　在弱类型的javascript中，我们还可以使用另一种方式实现调用`ng-app`的模型，就是使用自运行函数去调用，但是其原理还是一致的，就是通过给`module`传递ng-app的名称这样一个参数去进行模型的调用。