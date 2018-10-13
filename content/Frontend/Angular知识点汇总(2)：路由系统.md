---
title: "Angular知识点汇总(2)：路由系统"
date: 2018-10-13T19:21:36+08:00
tags: ["Angular"]
categories: ["Frontend"]
description : "Angular知识点汇总(2)：路由系统"
draft: false
---
<!-- TOC -->

- [1. 简介](#1-简介)
- [2. 生成带路由的项目](#2-生成带路由的项目)
- [3. Routes/RouterOutlet/RouterLink](#3-routesrouteroutletrouterlink)
  - [3.1. 配置路由参数](#31-配置路由参数)
  - [3.2. 绑定路由地址](#32-绑定路由地址)
  - [3.3. 路由通配符](#33-路由通配符)
- [4. Router](#4-router)
  - [4.1. 按钮点击跳转](#41-按钮点击跳转)
  - [4.2. 路由传递参数](#42-路由传递参数)
    - [4.2.1. 在查询字符串中传递参数](#421-在查询字符串中传递参数)
    - [4.2.2. 在路由路径中传递参数](#422-在路由路径中传递参数)
  - [4.3. 上面组件中获取参数的问题](#43-上面组件中获取参数的问题)
- [5. 重定向路由](#5-重定向路由)
  - [5.1. 场景](#51-场景)
  - [5.2. 示例](#52-示例)
- [6. 子路由](#6-子路由)
- [7. 辅助路由](#7-辅助路由)
- [8. 路由守卫](#8-路由守卫)
- [9. 参考资料](#9-参考资料)

<!-- /TOC -->

# 1. 简介

　　路由系统是Angular应用中不可或缺的组成，组件之间的导航关系都需要使用路由来进行规范，接下来先看一下Angular中的几个不同的路由对象，以及其作用：

| 路由对象         | 作用                                                           |
| :--------------- | :------------------------------------------------------------- |
| `Routes`         | 路由数据配置对象，保存URL与组件之间的映射关系                  |
| `RouterOutlet`   | 一个做占位符作用的指令，表示路由组件的内容在此展示             |
| `Router`         | 在应用运行时期可以操作路由的对象，例如跳转到其他的组件操作     |
| `RouterLink`     | 在HTML标签中声明导航URL的指令                                  |
| `ActivatedRoute` | 保存着当前激活路由数据的对象，可以用它获取路由地址、路由参数等 |


# 2. 生成带路由的项目

下面我们使用例子讲解各个路由的使用，重新生成一个带路由模板的基架项目：

```shell
ng new my-project-route --routing   # routing参数可以生成一个带路由模块的项目
```

使用带`routing`参数生成的项目会在`src/app`文件夹下面多出来一个文件`app-routing.module.ts`，应用中用到的路由都在这里进行配置。


也可以使用命令在已有项目中生成一个路由模块(同样生成名称为`app-routing`的路由模块)：

```shell
ng generate module app-routing --flat --module=app
```

生成的模块文件中有下面代码，需要自行增加导入代码([查看这里](https://www.angular.cn/tutorial/toh-pt5#add-routes))：
```typescript
import { NgModule }             from '@angular/core';
import { RouterModule, Routes } from '@angular/router';

@NgModule({
  exports: [ RouterModule ]
})
export class AppRoutingModule {}
```

# 3. Routes/RouterOutlet/RouterLink

为了演示首先生成两个组件：home/article:

```shell
ng g component home
ng g component article
```

## 3.1. 配置路由参数

然后我们配置一个路由，在根路由(根链接)下展示`home`组件，在`home`组件中可以跳转到`article`组件：
打开`app-routing.module.ts`，填入下面内容：

```typescript
import { ArticleComponent } from './article/article.component';
import { HomeComponent } from './home/home.component';
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

const routes: Routes = [
  {path: '', component: HomeComponent},
  {path: 'article', component: ArticleComponent}
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

- 路由是作为Angular模块的形式存在的，所以这里导入了`RouterModule`模块，然后在模块装饰器中import了`RouterModule`并且初始化了路由的配置`routes`。
- `Routes`需要一个数组参数，其中是多个对象：
  - `path`：表示该路由的访问路径，注意前面不需要加"`/`"。
  - `component`：表示该链接映射的组件，当路由到当前链接时，就会渲染该组件的内容。

## 3.2. 绑定路由地址

首先将`home`组件绑定到根组件中：
`app.component.html`：

```html
<h1>这是根组件中显示的标题</h1>
<a [routerLink]="['/']" >主页</a> <br>
<a [routerLink]="['/article']">文章</a>
<router-outlet></router-outlet>
```

- `[routerLink]`：这个中括号`[]`表示属性绑定，将一个数组绑定给`RouterLink`这个指令，最终会生成一个路由链接。绑定路由链接的方式还有一种简化的，我们在最后会说一下，因为`[routerLink]`这种属性绑定的方式更加灵活，自定义的能力更强，我们以这种方式作为例子，具体使用中可以根据需要选择。
- 这里绑定路径的时候需要注意的是在路径前面需要加上“`/`”，否则就会找不到页面。
- `<router-outlet`：该标签的作用是组件内容占位符，也就是当前路由的组件内容显示在这个位置。

然后分别修改`home`和`article`模板中的内容，以作区分。`ng serve`浏览一下看看效果：  
![主页](/image/Snipaste_2018-10-13_21-44-02.png)
![文章页](/image/Snipaste_2018-10-13_21-44-40.png)

## 3.3. 路由通配符

路由通配符是配置一个可以被任何`path`命中的路由参数，作用是当用户输入了一个在路由参数中未配置的`path`时，可以跳到一个指定的404页面，可以有更好的用户体验。
为此我们生成一个专门的404组件：
```shell
ng g component notfound
```

然后我们在`app-routing-module.ts`文件中配置通配符：

```typescript
...
const routes: Routes = [
  ...
  {path: '**', component: NotfoundComponent}
];
...
```

- 在此我们新增加了一个`path`为`**`的路由参数，`**`表示可以匹配所有的路径。
- 因为Angular的路由是从数组的0元素开始往后匹配的，所以**通配符配置必须放在最后一个**。

我们在notfound组件的模板加一个文字，看看效果：

```html
<h1>
  您访问的页面不存在，请返回<a [routerLink]="['/']" >首页</a>
</h1>
```

![not found](/image/Snipaste_2018-10-13_22-10-17.png)

# 4. Router

## 4.1. 按钮点击跳转

有些情况下需要在一个按钮点击的时候进行跳转，这时候就需要用到的`Router`的功能了。

我们将“文章”跳转链接改成按钮形式：
`app.component.html`:

```html
<h1>这是根组件中显示的标题</h1>
<a [routerLink]="['/']" >主页</a> <br>
<input type="button" value="文章" (click)="goToArticle()">
<router-outlet></router-outlet>
```

在组件`app.component.ts`中编写点击事件:

```typescript
...
export class AppComponent {
  /**
   * 依赖注入Router对象
   * @param router Router对象
   */
  constructor(private router: Router) {}
  /**
   * 跳转到文章页面
   */
  goToArticle() {
    this.router.navigate(['/article']);
  }
}
```

- `navigate`：`Router`对象的`navigate`函数可以导航到指定path的路由。

在浏览器中查看，可以实现同样的效果。

## 4.2. 路由传递参数

在组件中获取路由传递的参数，就需要使用`ActivatedRoute`对象。

### 4.2.1. 在查询字符串中传递参数

改写`app.component.html`的代码：

```html
<h1>这是根组件中显示的标题</h1>
<a [routerLink]="['/']" >主页</a> <br>
<a [routerLink]="['/article']" [queryParams]="{ id: 1 }">文章</a>
<router-outlet></router-outlet>
```

- `queryParams`：该属性绑定一个对象，用来在链接的查询字符串中增加数据。

接下来在`article.component.ts`组件中接收一下该参数：

```typescript
export class ArticleComponent implements OnInit {
  /**
   * 依赖注入ActivatedRoute对象，用来获取路由信息
   * @param routerInfo 当前激活路由对象
   */
  constructor(private routerInfo: ActivatedRoute) { }

  articleId: number;
  ngOnInit() {
    this.articleId = this.routerInfo.snapshot.queryParams['id'];
  }
}
```

在文章页面`article.component.html`中展示一下接收到的参数：

```html
<p>
  正在显示ID为：{{articleId}}的文章
</p>
```

在浏览器中查看效果：  
![参数](/image/Snipaste_2018-10-13_22-47-14.png)

### 4.2.2. 在路由路径中传递参数

修改`app.component.html`模板中的内容：

```html
<h1>这是根组件中显示的标题</h1>
<a [routerLink]="['/']" >主页</a> <br>
<a [routerLink]="['/article',1]">文章</a>
<router-outlet></router-outlet>
```

- 这里我们将参数值“1”，传入了`RouterLink`数组的第二个位置，第二个位置的参数就是路径参数。

修改`app.routing.module.ts`路由配置文件的配置：

```typescript
...
const routes: Routes = [
  {path: '', component: HomeComponent},
  {path: 'article/:id', component: ArticleComponent},
  {path: '**', component: NotfoundComponent}
];
...
```

- 这里我们在原先`path`的基础上增加了一个参数语法“`:id`”，就会形成路径参数的匹配。

在`article.component.ts`组件中接收该路径参数(只需将`queryParams`改成`params`)：

```typescript
export class ArticleComponent implements OnInit {
  /**
   * 依赖注入ActivatedRoute对象，用来获取路由信息
   * @param routerInfo 当前激活路由对象
   */
  constructor(private routerInfo: ActivatedRoute) { }

  articleId: number;
  ngOnInit() {
    this.articleId = this.routerInfo.snapshot.params['id'];
  }
}
```

在浏览器中浏览可以看到效果：    
![参数](/image/Snipaste_2018-10-13_22-56-19.png)

## 4.3. 上面组件中获取参数的问题

我们在`app.component.html`加一个a链接，同样跳转到文章页面，这个链接的参数是2：

```typescript
<h1>这是根组件中显示的标题</h1>
<a [routerLink]="['/']" >主页</a> <br>
<a [routerLink]="['/article',1]">文章</a>
<a [routerLink]="['/article',2]">文章2</a>
<router-outlet></router-outlet>
```

其他的都不需要修改，我们打开浏览器看看这个没有提到的问题在哪：  
![问题](/image/Snipaste_2018-10-13_23-07-03.png)
从上面的图中可以看到，不管路径参数是1还是2，我们在文章页面中显示的始终是1，也就是我们在组件中获取`params['id']`的值始终是1，这是怎么回事呢？怎么解决这个问题？

首先我们看看在`article.component.ts`组件中获取参数值的那行代码：

```typescript
this.articleId = this.routerInfo.snapshot.params['id'];
```

　　上面图片中出现的问题就是`snapshot`这个属性造成的，这个`snapshot`获取的参数叫做快照参数(从它的名字就可大概猜出)，也就是在组件`OnInit`方法被第一次调用的时候，它会保存一个参数值，以后无论参数如何修改它都不会得知有新的参数值，始终是这一个，这就是我们看到上面图片中展示的问题。同样的当你第一次点击的链接是“文章2”那么id的值就始终是2了。

　　要解决这个问题，我们需要换一种获取参数的方式，叫做订阅参数，顾名思义就是订阅参数的变化，每次进来获取的都是最新的参数值。

修改`article.component.ts`组件的接收方法：

```typescript
...
ngOnInit() {
    this.routerInfo.params.subscribe((params: Params) => {
      this.articleId = params['id'];
    });
}
...
```

- 这里我们将`snapshot`换成了`params.subscribe()`。
- `subscribe()`函数接收一个匿名函数，会传入一个存在于`@angular/router`模块中的`Params`对象，它里面存储着最新的路由参数。

我们在看浏览器效果是不是没有问题了：  
![问题](/image/Snipaste_2018-10-13_23-23-08.png)

# 5. 重定向路由

## 5.1. 场景

有些情况下，我们的主页是一个单独的组件页面，也就是通过路径`/home`来访问的，这时想要根链接默认也打开`/home`路径的组件，那么就可以用到的重定向路由来解决了。

## 5.2. 示例

我们修改一下`app.routing.module.ts`路由配置，增加一个单独的`home`路由，并且将根链接重定向到`home`路由：

```typescript
...
const routes: Routes = [
  {path: '', redirectTo: '/home', pathMatch: 'full'},
  {path: 'home', component: HomeComponent},
  {path: 'article/:id', component: ArticleComponent},
  {path: '**', component: NotfoundComponent}
];
...
```

- `redirectTo`：该参数指定重定向到那个路径，注意这里的是链接路径，需要在前面加上`/`符号。
- `pathMatch`：路径匹配方式：
  - `full`是完全匹配，例如当重定向路径设置为`/home`，那么`/home/123`就不会匹配到。
  - `prefix`是前缀匹配。例如当重定向路径设置为`/home`，那么包含`/home`这个前缀的路径`/home/123`也会被匹配到。

# 6. 子路由

# 7. 辅助路由

# 8. 路由守卫

# 9. 参考资料

[https://www.angular.cn/guide/router](https://www.angular.cn/guide/router)  
[https://www.angular.cn/tutorial/toh-pt5](https://www.angular.cn/tutorial/toh-pt5)