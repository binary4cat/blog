---
title: "Angular知识点汇总(5)：HTTP服务通信"
date: 2018-10-25T21:04:40+08:00
tags: ["Angular"]
categories: ["Frontend"]
description : "Angular知识点汇总(5)：HTTP服务通信"
draft: false
---

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

# 2. HttpClientModule模块

## 2.1. HttpClientModule模块介绍

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
- `HttpClient`是Angular中最新的HTTP请求封装类型，是在4.3.0版本引入的，它和以前的`Http`类有比较大的区别，总体使用更加方便了，你可以看[这篇文章](https://blog.angularindepth.com/the-new-angular-httpclient-api-9e5c85fe3361)了解新旧封装类型的变化，以及了解`HttpClient`的使用，我在接下来会简单的说一下使用需要注意的地方。

## 2.2. HttpClientModule的使用

在这里分别演示一个GET/POST请求的使用，调用的后端接口是一个简单的Go语言的接口实现，代码在[这里](https://gist.github.com/hjdo/4f0b0a87c1b289b1fe079a44d121f6c1)可以找见。

### 2.2.1. Get请求演示

**Component:**

```typescript
export class TestHttpComponent implements OnInit {
  getService: Observable<UserInfo>;
  constructor(private http: HttpClient, private fb: FormBuilder) {
    this.getService = this.http.get<UserInfo>('http://localhost:2333/get');
  }
  /**
   * 获取用户信息按钮事件
   */
  getUserInfo() {
    this.getService.subscribe((data: UserInfo) => {
      console.log(data);
    });
  }
}

/**
 * 用户信息
 */
export class UserInfo {
  constructor(
    public Email: string,
    public Password: string) {}
}
```

- 首先定义了一个名为`getService`的`Observable<UserInfo>`可观察的流对象，泛型中的类型为我们定义的已知数据对象。
    - 该`getService`我们单独的拿出来，要说明一下在正式的开发中，我们会将这种对后端的请求函数，会封装成一个service类，里面有各种的请求方法，在需要使用的地方直接调用即可，下面演示一下`getUserInfo`封装后的代码：

    ```typescript
    // UserInfo.service.ts

    getUserInfo<T>(){
        return this.http.get<T>('http://localhost:2333/get');
    }
    ```

    - 我们将`getUserInfo`封装到了一个类中，它会返回一个可订阅的流对象`Observable<T>`，注意这里只是返回了一个可订阅的流，并没有真正的发起请求，真正的请求会在流被`subscribe`订阅的时候发生。

- 在真正的点击事件中，我们订阅了这个流，此时就会发起GET请求，从后端接口返回数据，这里我们打印出来了：
  
![返回](/image/Snipaste_2018-11-05_22-49-11.png)

### 2.2.2. POST请求演示

**Component:**

```typescript
export class TestHttpComponent implements OnInit {
  constructor(private http: HttpClient, private fb: FormBuilder) {
    // 组织
    this.loginForm = fb.group({
      email: [],
      password: []
    });
  }

  postService: Observable<any>;
  loginForm: FormGroup;

  postServiceFunc(userInfo: UserInfo) {
    return this.postService = this.http.post<boolean>('http://localhost:2333/login', userInfo);
  }

  submit() {
    const userInfo = new UserInfo(
      this.loginForm.value.email,
      this.loginForm.value.password
    );
    this.postServiceFunc(userInfo).subscribe(res =>
      console.log(res)
    );
  }
}
```

- 页面上是一个表单，填写`email`和`password`点击提交就会触发`submit`函数。
- 这里我们定义的“service”是类似的，只不过请求的方法变为了`post`方法，并且多了一个参数。
- 传入的参数默认会被序列化成json格式传递。

![post](/image/Snipaste_2018-11-05_23-24-13.png)

## 2.3. 配置后端API请求地址

上面的两个例子中我们都将后端的接口地址写全了，但是在实际的开发中可能会用到多个后端的域名，这是因为在测试环境和发布环境肯定会调用不同的后端地址，但是如果像上面例子中写死的话，我想没有人会去挨个修改，所以我们需要将后端的域名(也就是上面例子的`http://localhost:2333`)作为配置变量，以便在需要改动的时候只修改一次。

首先我们需要在项目的根目录下创建一个json文件，文件名一般为`proxy.conf.json`，当然你可以自定义文件名，其内容为：

```json
{
  "/api": {
    "target": "http://localhost:2333"
  }
}
```

- 在json文件中，我们定义了一个`/api`名称的变量，它的值是一个对象，其中包含一个`target`属性，也许从名字你就能大概猜出来了，它的作用是将Angular中的包含`/api`的请求，转发到`target`所代表的地址。
- 需要注意的是，上面描述的是转发而不是替换，也就是将`/api/get`这个请求发往`http://localhost:2333/api/get`。
- 所以这点是不同的，我们之前的例子里的路径是没有`api`这个字符的，但是我们需要使用`api`代表转发，所以后端的接口路径也需要包含`api`字符。
- 更多的内容可以查阅[这里](https://github.com/angular/angular-cli/blob/master/docs/documentation/stories/proxy.md)

然后我们将之前的请求地址都改成`/api/get`、`/api/login`这两个字符串。

最后我们需要在服务启动的时候，让配置生效，需要用到`--proxy-config`参数，它会使指定的代理配置生效：

```shell
ng serve --proxy-config proxy.conf.json
```

启动后，我们尝试刚才的POST请求，可以看到请求依然是正确执行的。但是我们看`Network`请求的信息会发现Request URL的地址是Angular的链接，但是请求确实发送到了后端API，这点有点疑惑，如果你对这里有所了解，麻烦留言给我😊

![Network](/image/Snipaste_2018-11-06_22-28-25.png)

上面的启动命令写起来会比较麻烦，每次都需要写一大串，我们可以换种方式，调用npm的脚本启动。
打开`package.json`文件，将其中`script`节点中的`start`的值改为我们刚才的启动命令：

```json
-- "start": "ng serve",
++ "start": "ng serve --proxy-config proxy.conf.json",
```

以后每次启动我们就可以直接使用`npm start`来启动了。

## 2.4. HTTP请求Header和参数设置

在`HttpClient`模块中，所有的请求方法最后一个参数都是`options`，这个参数十分重要，它里面配置了我们请求携带的参数，以及返回的数据类型。
这里要说的`Header`就是`options`中的一个属性，在我们需要请求携带身份验证信息的时候，就需要设置Header：

```typescript
const httpOptions = {
  headers: new HttpHeaders({
    'Content-Type':  'application/json',
    'Authorization': 'my-auth-token'
  })
};

this.http.post<Hero>(url, data, httpOptions)
```

## 2.5. 异步管道的方式获取数据

上面的例子中，我们都是模拟定义一个service，然后再点击事件中使用`subscribe`触发HTTP请求的调用，如果一个请求只是单纯的返回一些可以直接使用的数据的话，Angular给我们提供了一个异步管道`async`，使用它我们可以直接再模板中，发起请求并接收数据。

加入有一个`GetUserInfos`请求，它返回一个用户信息的集合，我们需要把这个集合循环展示在页面。

在Component中增加一个值`Observable<Array<UserInfo>>`属性：

```typescript
userArrService: Observable<Array<UserInfo>>;
constructor(private http: HttpClient, private fb: FormBuilder) {
  this.userArrService = http.get<Array<UserInfo>>('api/getuserinfo');
}
```

然后在页面中直接使用`async`异步管道去请求数据：

```html
<ul>
    <li *ngFor="let user of userArrService | async">
        {{user.email}} {{user.password}}
    </li>
</ul>
```

- 我们直接在`ngFor`中循环了`userArrService`这个流，由于借助了`async`管道，它会调用这个流，并且返回流中的数据，使得我们的`ngFor`正常渲染。
- 当然使用场景不仅是在`ngFor`，其他可以在页面直接渲染的流，都可用`async`解决，会很方便。

## 2.6. 高级用法

Angular的HTTP通信组件非常强大，还有很多实用的高级用法，[官方文档](https://www.angular.cn/guide/http#advanced-usage)非常的详细，我没有办法再精简总结它，你可以浏览官方文档的这部分，以便在合适的场景下使用。

# 3. 编写可订阅的WebSocket通信

我们可以将websocket封装成一个可订阅的`Observable<any>`，这样的话我们在调用的时候，就可以直接订阅这个流就可以了。

```typescript
ws: WebSocket;
getObservableWebSocket(url): Observable<any> {
    this.ws = new WebSocket(url);
    return new Observable(ob => {
      this.ws.onmessage = (e) => ob.next(e.data);
      this.ws.onerror = (e) => ob.error(e);
      this.ws.onclose = (e) => ob.complete();
    });
}
```

- 我们定义了一个公用的方法，用来创建一个可订阅的WebSocket流对象。
- 可以看到我们将WebSocket的三个事件分别添加到了流`Observable`的三个自定义函数。
    - 第一个函数监听WebSocket的`onmessage`事件，当收到来自服务端的消息时，会触发`next`函数，将服务端发送的数据发射出去。
    - 第二个函数监听`onerror`事件，当WebSocket发生错误的时候，会触发该事件，从而调用`error`函数，将错误传递出去。
    - 第三个函数监听`onclose`事件，当WebSocket通信关闭的时候，会触发该事件，，从而调用`complete`函数，通知订阅者流结束了。

我们在使用的时候，就可以直接调用这个方法，然后使用`subscribe`函数订阅，并且处理这三个事件：

```typescript
this.getObservableWebSocket('ws:localhost:2333').subscribe(
    data => console.log('服务端返回了数据：' + data),
    err => console.log('websocket通信发生错误' + err),
    () => console.log('websocket关闭')
);
```

- 在使用的时候，直接调用该方法，然后传入服务端地址，最后订阅并且处理返回的流即可。

# 4. 参考资料

[https://www.angular.cn/guide/rx-library](https://www.angular.cn/guide/rx-library)  
[https://www.angular.cn/guide/observables](https://www.angular.cn/guide/observables)  
[https://www.angular.cn/guide/practical-observable-usage](https://www.angular.cn/guide/practical-observable-usage)  
[https://blog.angularindepth.com/the-new-angular-httpclient-api-9e5c85fe3361](https://blog.angularindepth.com/the-new-angular-httpclient-api-9e5c85fe3361)  
[https://www.angular.cn/guide/http](https://www.angular.cn/guide/http)  
[https://webpack.js.org/configuration/dev-server/#devserver-proxy](https://webpack.js.org/configuration/dev-server/#devserver-proxy)  
[https://github.com/angular/angular-cli/blob/master/docs/documentation/stories/proxy.md](https://github.com/angular/angular-cli/blob/master/docs/documentation/stories/proxy.md)  
