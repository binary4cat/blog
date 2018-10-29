---
title: "Angular知识点汇总(5)：HTTP服务通信"
date: 2018-10-25T21:04:40+08:00
tags: ["Angular"]
categories: ["Frontend"]
description : "Angular知识点汇总(5)：HTTP服务通信"
draft: false
---
<!-- TOC -->

- [1. 响应式编程](#1-响应式编程)
  - [1.1. 名词解释](#11-名词解释)
  - [1.2. 简单例子](#12-简单例子)
  - [1.3. 在Angular中使用响应式编程](#13-在angular中使用响应式编程)
- [HttpClientModule模块](#httpclientmodule模块)
  - [HttpClientModule模块介绍](#httpclientmodule模块介绍)
  - [HttpClientModule的使用](#httpclientmodule的使用)
  - [配置后端API请求地址](#配置后端api请求地址)
  - [HTTP请求Header和参数设置](#http请求header和参数设置)
  - [异步管道的方式获取数据](#异步管道的方式获取数据)
  - [请求的防抖](#请求的防抖)
- [WebSocket通信](#websocket通信)
- [2. 参考资料](#2-参考资料)

<!-- /TOC -->

# 1. 响应式编程

在开始写HTTP通信之前，先简单的说一个必要的知识点：响应式编程，因为在Angular的HTTP通信中，返回的结果通常都是一个可观察对象，而响应式编程就是一个观察者模式(可观察对象)的实现。

## 1.1. 名词解释

- 可观察对象（Observable）：可观察对象基本上可以理解为观察者订阅者模式，可观察对象可以发送多种类型的值(字面量、消息、事件)，无论这些值是同步发送还是异步发送的，我们都无需在意，只需要订阅这个可观察对象，并且消费它的数据即可，消费完取消订阅，无需其他的操作。
- 响应式编程：响应式编程是一种面向数据流和变更传播的异步编程范式（[Wikipedia](https://zh.wikipedia.org/wiki/%E5%93%8D%E5%BA%94%E5%BC%8F%E7%BC%96%E7%A8%8B)）。RxJS（响应式扩展的 JavaScript 版）是一个使用可观察对象进行响应式编程的库，它让组合异步代码和基于回调的代码变得更简单 ([RxJS Docs](http://reactivex.io/rxjs/))。RxJS 提供了一种对 Observable 类型的实现。

## 1.2. 简单例子

下面的代码基于rxjs6版本，6之前的版本语法差异较大，请自行查阅文档。

```typescript
import { from } from 'rxjs';
import { filter } from 'rxjs/operators';

viewObservable(obj: any) {
    const names: string[] = ['zhangsan', 'lisi', 'wangwu', 'zhaosi'];
    from(names).pipe(
      filter(f => f.indexOf('san') >= 0,
    )).subscribe(
      result => console.log(result),
      error => console.error(error),
      () => console.log('订阅结束')
    );
}
```

- 在Angualr中使用的响应式编程实现，都是来自于rxjs库。
- 上面的代码演示了一个使用响应式编程处理数组的例子，`from`函数创建一个可观察对象，这里我们观察的是一个字符数组。
- `pipe`表示可观察对象的处理管道，其中可以包含多个处理函数，例如`filter`、`map`等。
- `subscribe`表示订阅，用来进行最终的处理通知，它包含了三个匿名函数(严格按照顺序)：
    1. 结果函数：该函数是必选的，用来处理最终的返回结果，可以自行实现个性化的处理，例子里面是打印了结果。
    2. 错误处理函数：该函数可选，它收一个错误对象，用来在发生错误的时候进行处理。
    3. 结束时调用：该函数可选，它会在订阅结束的时候调用一次。

## 1.3. 在Angular中使用响应式编程

接下来演示一个可观测的事件流的例子，将事件当作流来处理，可以理解成该事件是一个永不结束的流，避免频繁的事件触发调用。因为在普通的事件绑定中都是触发一次调用一次，所以事件流的好处就体现出来了。

```html
// template

<input [formControl]="checkInput">
```

```typescript
// component

import { delay } from 'rxjs/operators';
export class TestHttpComponent implements OnInit {
  checkInput: FormControl = new FormControl();
  
  constructor() {
    this.checkInput.valueChanges.pipe(
      delay(1000)
    ).subscribe(
      input => console.log(input)
    );
  }
}
```

- `FormControl`对象的`valueChanges`属性的结果是一个可观察对象Observable，它会在每次“value”发生改变的时候发布一次流，如果有观察者订阅了这个流，那么就会触发监听事件调用。
- 我们在构造函数中订阅了这个事件流，当页面上每次输入的内容变化时，就会延迟(`pipe`中的`delay`函数)1秒钟将value打印在控制台。

![observable](/image/20181025observable.gif)

# HttpClientModule模块

## HttpClientModule模块介绍

Angular的`HttpClient`类封装了很多的请求方法，例如`POST`/`GET`/`PUT`/`DELETE`/`HEAD`等等众多方法。每一个方法返回的都是一个`Observable<T>`的响应流（`T`的类型众多，这里用来做代替），在请求的返回阶段我们需要订阅这个流，获取服务端响应的数据或者错误信息。

```typescript
request(method: string, url: string, options: {
    body?: any;
    headers?: HttpHeaders | {
        [header: string]: string | string[];
    };
    observe?: 'body';
    params?: HttpParams | {
        [param: string]: string | string[];
    };
    reportProgress?: boolean;
    responseType: 'blob';
    withCredentials?: boolean;
}): Observable<Blob>;

get(url: string, options: {
    headers?: HttpHeaders | {
        [header: string]: string | string[];
    };
    observe: 'response';
    params?: HttpParams | {
      [param: string]: string | string[];
    };
    reportProgress?: boolean;
    responseType: 'arraybuffer';
    withCredentials?: boolean;
}): Observable<HttpResponse<ArrayBuffer>>;
```

- 上面从`HttpClient`类的定义中摘选了两个方法，方法中的参数大致都是相同的，请求的方式可以指定或者调用专用的方法。
- 其中可以看到参数`options`时相同的，包含一致的属性，其中的作用可以从属性名大致看出来，其中影响`Observable<T>`的`T`类型的属性是`responseType`，因为我们最终返回的流中的数据类型应该是我们的`responseType`类型。

## HttpClientModule的使用

## 配置后端API请求地址

## HTTP请求Header和参数设置

## 异步管道的方式获取数据

## 请求的防抖

# WebSocket通信



# 2. 参考资料

[https://www.angular.cn/guide/rx-library](https://www.angular.cn/guide/rx-library)  
[https://www.angular.cn/guide/observables](https://www.angular.cn/guide/observables)  
[https://www.angular.cn/guide/practical-observable-usage](https://www.angular.cn/guide/practical-observable-usage)  