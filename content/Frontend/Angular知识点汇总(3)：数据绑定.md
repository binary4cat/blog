---
title: "Angular知识点汇总(3)：数据绑定"
date: 2018-10-16T22:36:15+08:00
tags: ["Angular"]
categories: ["Frontend"]
description : "Angular知识点汇总(3)：数据绑定"
draft: false
---
<!-- TOC -->

- [1. 事件绑定](#1-事件绑定)
    - [1.1. 事件绑定的语法](#11-事件绑定的语法)
    - [1.2. 组件中绑定的方法](#12-组件中绑定的方法)
    - [1.3. 模板局部变量](#13-模板局部变量)
- [2. 属性绑定](#2-属性绑定)
    - [2.1. HTML属性绑定和DOM属性绑定之间的区别](#21-html属性绑定和dom属性绑定之间的区别)
    - [2.2. DOM属性绑定](#22-dom属性绑定)
    - [2.3. HTML属性绑定](#23-html属性绑定)
    - [2.4. 双向绑定](#24-双向绑定)
- [3. 响应式编程介绍](#3-响应式编程介绍)
- [4. 管道(pipe)](#4-管道pipe)
- [5. 参考资料](#5-参考资料)

<!-- /TOC -->

# 1. 事件绑定

## 1.1. 事件绑定的语法

事件绑定的语法：

```html
<input (input)="onInputEvent($event)">
<button (click)="onClickEvent()">
```

- `(eventName)`这种小括号包含事件名称的语法就是angular的事件绑定语法，可以将该事件绑定到组件中一个方法，该事件触发时，绑定的方法就会被执行。
- `<input (input)="onInputEvent($event)">`
    - `(input)`：事件绑定语法，绑定的是DOM的原生事件。
    - `onInputEvent`：组件中的一个方法名称，该事件触发的时候就会调用这个方法了。
    - `$event`：浏览器的事件对象，包含当前事件的数据。

## 1.2. 组件中绑定的方法

```typescript
...
export class TestComponent implements OnInit {
  constructor() { }
  ngOnInit() {}
  onInputEvent(event){
      console.log(`您正在输入的内容：${event.target.value}`)
  }
}
```

- 当输入事件触发的时候就会调用组件中的`onInputEvent`函数，在这里我们将输入的内容打印出来。
- 可以看到方法中接收一个浏览器的事件对象，要获取输入的值需要有两次调用`.target.value`。

## 1.3. 模板局部变量

上面的事件参数(`$event`)输入的是原生的浏览器事件对象，有时我们只需要它的value就可以，这时我们可以使用angular提供的模板局部变量，语法的格式是：`<input #valName>`。

在这里定义两个HTML标签，分别打印浏览器事件对象和模板局部变量：

```html
<input (input)="onInputEvent($event)">
<input #myInput (input)="onInputEvent(myInput)" id="myInput">
```

![模板局部变量](/image/Snipaste_2018-10-16_23-05-48.png)

- 可以看到浏览器事件对象的属性较多，如果有需要更多数据的话可以使用该变量。
- 而模板局部变量只是一个DOM的引用，相当于是`document.getElementById("myInput")`的引用。

在只需要传递值的时候，可以使用模板局部变量：

```html
<input #myInput (input)="onInputEvent(myInput.value)">
```

- 直接传入value值即可。
- 要注意写法：声明的时候要加`#`号(`#myInput`)，在取参的时候不需要`#`号(`myInput.value`)。

# 2. 属性绑定

## 2.1. HTML属性绑定和DOM属性绑定之间的区别

用例子演示一下：

```html
// template
<input value="123" (input)="onInputEvent($event)">

// component
onInputEvent(event: any) {
    console.log(`DOM属性value的值为：${event.target.value}`);
    console.log(`HTML属性value的值为：${event.target.getAttribute('value')}`);
}
```

输出效果：  
![输出](/image/Snipaste_2018-10-16_23-30-02.png)

- HTML属性在页面渲染完成后就确定下来了，以后就不再改变了，有点类似于上一篇说过的快照参数。
- DOM属性是当前DOM对象的属性，属于是浏览器的一员，所以总是反映当前属性的状态(值)。
- HTML属性的值不能改变，但是DOM属性的值是可以改变的。
- 因为DOM属性可变这个特性，模板绑定是通过DOM属性和事件结合来实现的。

## 2.2. DOM属性绑定

## 2.3. HTML属性绑定

## 2.4. 双向绑定

# 3. 响应式编程介绍

# 4. 管道(pipe)

# 5. 参考资料

[https://my.oschina.net/u/3412211/blog/896636](https://my.oschina.net/u/3412211/blog/896636)  
[https://www.angular.cn/guide/displaying-data](https://www.angular.cn/guide/displaying-data)  
[https://www.angular.cn/guide/pipes](https://www.angular.cn/guide/pipes)  