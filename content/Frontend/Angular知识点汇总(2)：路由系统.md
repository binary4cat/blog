---
title: "Angular知识点汇总(2)：路由系统"
date: 2018-10-13T19:21:36+08:00
tags: ["Angular"]
categories: ["Frontend"]
description : "Angular知识点汇总(2)：路由系统"
draft: false
---

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

组件之间是可以互相嵌套的，这就有一个问题，怎么路由到组件中嵌套的组件，这就需要用到子路由来实现。

创建两个新的组件嵌套在`home`组件中：

```shell
ng g component category
ng g component tag
```

首先我们把这两个组件作为子路由的组件，绑定到`home`组件下面，编辑`app-routing-module.ts`文件：

```typescript
const routes: Routes = [
  ...
  {path: 'home', component: HomeComponent, children: [
    {path: '', component: CategoryComponent},
    {path: 'tags', component: TagComponent}
  ]},
  ...
];
```

- `children`：该属性是声明子路由的属性，它的值是一个数组，可以包含多个路由参数对象，子路由的参数对象跟普通的路由参数对象无二，只是包含在`children`属性下。

然后编辑`home.component.html`模板文件，将子路由的链接绑定到页面，并且添加一个占位符`<router-outlet>`，用来显示子路由的内容：

```html
<div class="sty">
  <p>
    这里是主页
  </p>
  <a [routerLink]="['./']">查看文章分类</a><br>
  <a [routerLink]="['./tags']">查看文章标签</a>
  <router-outlet></router-outlet>
</div>
```

- 特别注意：在绑定子路由的路径的时候，**必须在最前面加一个`.`符号**，这表明当前的路由是一个子路由，就会去子路由的列表中查找，否则机会在根路由中查找。

来看一下在浏览器中的效果(为了区分组件的嵌套关系，我加了一点背景颜色):  
![效果](/image/Snipaste_2018-10-14_00-35-34.png)

因为我在子路由中设置的路径为空的时候显示`category`组件，所以可以看到在`/home`路径下默认展示的子组件是`category`，当点击“查看文章标签”按钮后，浏览器链接就会在`/home`路径后追加子路由的路径`/tags`，并且页面也渲染出了`tag`组件的内容。

# 7. 辅助路由

## 辅助路由介绍
　　到目前为止，我们在页面上每次只能显示一个路由组件，那如果有些场景下，在一个页面上需要显示两个路由组件，这个需要怎么控制呢？我们可以看下面的代码，思考一下：

```html
<a [routerLink]="['./']">查看文章分类</a><br>
<a [routerLink]="['./tags']">查看文章标签</a>
<router-outlet></router-outlet>
<router-outlet></router-outlet>
```

假设第一个`router-outlet`占位符是展示文章其他内容(例如最近文章的)，而下面一个占位符才是我们想要展示文章分类或者文章标签的，怎么精准控制组件显示在哪里，就需要用到辅助路由的功能。

## 辅助路由的编写

为了演示，我们生成两个新的组件`auxone`和`auxtwo`。接下来首先我们修改一下`app.routing-module.ts`文件中的路由配置：

```typescript
...
const routes: Routes = [
  {path: '', redirectTo: '/home', pathMatch: 'full'},
  {path: 'home', component: HomeComponent, children: [
    {path: '', component: CategoryComponent},
    {path: 'tags', component: TagComponent}
  ]},
  {path: 'auxone', component: AuxoneComponent, outlet: 'tagAndCategory'},
  {path: 'auxtwo', component: AuxtwoComponent, outlet: 'tagAndCategory'},
  {path: 'article/:id', component: ArticleComponent},
  {path: '**', component: NotfoundComponent}
];
...
```

- 可以看到在原先的基础上增加了两个新的组件的路由参数，而这两个组件路由参数多了一个`outlet`属性，该属性标注了一个“名称”，也就是当前的组件显示在哪个名称的插槽`router-outlet`上。

接下来我们在`app.component.html`主组件的模板中加入跳转的链接以及插槽：

```html
<h1>这是根组件中显示的标题</h1>
<a [routerLink]="['/home']" >主页</a><br>
<a [routerLink]="['/article',1]" >文章</a><br>

<a [routerLink]="[{outlets:{tagAndCategory:['auxone']}}]">auxone</a> <br>
<a [routerLink]="[{outlets:{tagAndCategory:['auxtwo']}}]">auxtwo</a><br>

<div style="background-color:blue;height:300px;padding: 30px">
  <router-outlet></router-outlet>
</div>
<div style="background-color:red;height:300px;padding: 30px">
  <router-outlet name="tagAndCategory"></router-outlet>
</div>
```

- 在原先的基础上增加了两个a标签，这两个a标签的`routerLink`参数只有一个对象，而这个对象的属性`outlets`的值也是个对象，里面保存了命名的“路由名称”对应哪个“组件的路径”。
- 在最后我们又声明了一个插槽，不过这个插槽的有一个name属性，它的值就是前面都要用到的路由的“命名”。
- 为了区分两个插槽的位置，我增加了一个背景色。

然后浏览一下看看效果：

- 首先打开home主页,可以看到和之前的没什么区别(多了两个链接按钮和样式)： 
![主页](/image/Snipaste_2018-10-14_19-16-37.png)
- 接着打开文章页，除了加了背景颜色没有其他的区别：
![文章页](/image/Snipaste_2018-10-14_19-18-12.png)
- 这时点击的内容始终都在第一个插槽的位置显示，我们现在点击`auxone`按钮和`auxtwo`按钮看看：
![aux](/image/Snipaste_2018-10-14_19-21-23.png)
  - 可以看到点击这两个按钮的时候，不会影响第一个插槽位置显示的内容，只会改变第二个插槽位置的内容。
  - 可以看到浏览器的链接很奇怪，这正是用来标注辅助路由的链接，你可以复制这个链接在新页面打开，路由页依然会找到正确的位置。
- 这时我们在点击“主页”按钮：
![主页](/image/Snipaste_2018-10-14_19-25-28.png)
  - 可以看到链接部分的变化，只是括号前面的路径发生了变化，括号内表示辅助路由的标记并没有跟着变，再看页面的内容也是只改变了第一个插槽位置的内容。

## 在辅助路由跳转时控制主路由显示

辅助路由还有一个功能，就是可以在辅助路由跳转的同时，控制主路由显示哪一个路径，只需要在`routerLink`中多增加一个`primary`属性参数：

```html
<a [routerLink]="[{outlets:{primary:'home',tagAndCategory:['auxone']}}]">auxone</a> <br>
```

- 上面的代码作用是在点击"auxone"按钮时，让主路由跳转到“home”路径映射的组件内容。

在浏览器中测试一下，我们先进入到“文章”页面，然后点击“auxone”按钮，可以看到除了在第二个插槽的位置显示了辅助路由内容，还将第一个插槽位置的内容切换为了“主页”的内容。

# 8. 路由守卫

## 路由守卫简介
路由守卫其实是Angular路由系统释放的hook(钩子)，用来在路由导航的各个时期插入我们自定义的操作，具体的应用例如在用户想要进入某个需要权限才能访问的页面时，可以做一些自定义的限制。

路由系统的三个hook(接口)：

| HOOK               | 注释                                                                    |
| :----------------- | :---------------------------------------------------------------------- |
| `CanActivate`      | 进入某个路由之前触发的hook                                              |
| `CanActivateChild` | 进入某个子路由之前触发的hook                                            |
| `CanDeactivate`    | 从某个路由离开前触发的hook                                              |
| `Resolve`          | 在路由被激活之前获取路由数据                                            |
| `CanLoad`          | [异步导航到某特性模块时触发](https://www.angular.cn/api/router/CanLoad) |

## 实现路由守卫功能的要点

　　要实现一个自定义的路由守卫，需要自行创建一个`ts`文件，然后继承上面的接口，实现相应的方法。然后将该自定义的`class`交给路由模块的提供器，并且标注到对应的路由参数中。  
　　路由守卫实现方法除了`Resolve`之外，都需要返回一个`bool`值，返回true表示路由可以继续导航，返回false就会中断当前的导航，用户会留在原地。

## `CanActivate`

我们使用cli命令生成一个普通的ts类编写进入“文章页面”的验证，命名为`LoginGuard`：

```shell
ng g class routerGuards/loginGuard
```

修改文件`login-guard.ts`文件的内容：

```typescript
import { CanActivate, ActivatedRouteSnapshot, Router } from '@angular/router';
import { Injectable } from '@angular/core';

@Injectable()
export class LoginGuard implements CanActivate {
    constructor(private router: Router) { }

    canActivate(route: ActivatedRouteSnapshot) {
        if (route.params['id'] === '1') {
            alert(`Id为${route.params['id']}的文章您无权访问`);
            this.router.navigate(['/home']);
            return false;
        } else {
            return true;
        }
    }
}
```

- `canActivate`方法有两个参数，分别是`ActivatedRouteSnapshot`和`RouterStateSnapshot`，这两个名字可以看出来和之前的快照有关系，也就是在路由导航时获取路由参数和状态，因为这里我只用到了一个`ActivatedRouteSnapshot`,所以只声明了它一个，在tslint检测到没有用到的变量时会报错，所以另一个就不能写了。
- 在构造函数中注入了`Router`对象，可以在验证失败的时候导航到指定的页面。而为了在自定义的类中依赖注入对象，应该在类上声明`@Injectable()`装饰器。
  - 组件模块为什么不需要声明`@Injectable()`装饰器就能注入呢？因为组件、模块等装饰器其实就是继承的`@Injectable()`装饰器，所以他们自身就有提供依赖注入的能力。
- 在`canActivate`函数内部，在验证失败的时候，先导向其他的路由，然后返回false，这个顺序不能乱。

现在打开路由配置`app.routing.module.ts`，将路由守卫加到`article`路由的配置中：

```typescript
const routes: Routes = [
  ...
  {path: 'article/:id', component: ArticleComponent, canActivate: [LoginGuard]},
  ...
];

@NgModule({
  ...
  providers: [LoginGuard]
})
export class AppRoutingModule { }
```

- `Routes`参数中，给文章路由新增加了一个`canAactivate`属性，它的值是一个数组，数组中是自定义的路由守卫。 它的值是数组就说明可以有多个路由守卫，但是注意的是如果是多个，那么需要所有的都返回true那么路由才会导航进去。
- 然后在模块装饰器的提供器`providers`中注入路由守卫。

在浏览器浏览，我们直接在浏览器中中输入`http://localhost:4200/article/1`，可以看到被阻止进入页面：  
![阻止](/image/Snipaste_2018-10-14_20-35-17.png)

## `CanDeactivate`

在离开当前的页面(路由)时，可能需要给用户做出一些提示，例如提示表单未提交之类的操作，那么`CanDeactive`hook就可以实现该功能。

同样的需要创建一个自定义的class来编写路由守卫：

```shell
ng g class routerGuards/unSaveGuard
```

打开`un-save-guard.ts`文件，编写内容：

```typescript
import { HomeComponent } from './../home/home.component';
import { CanDeactivate } from '@angular/router';

export class UnSaveGuard implements CanDeactivate<HomeComponent> {
    canDeactivate(home: HomeComponent) {
        if (!home.canQuit) {
            return window.confirm('确定要离开吗？');
        } else {
            return true;
        }
    }
}
```

- `CanDeactivate`需要一个泛型参数，通常是需要保护的路由组件，通过组件内部的一些属性值，或者方法返回判断是否应该提示用户。在这里我演示打开页面的10秒内离开“主页”会提示用户：
`home.component.ts`:
```typescript
export class HomeComponent implements OnInit {
  constructor() { }

  canQuit: boolean;
  ngOnInit() {
    setTimeout(() => {
      this.canQuit = true;
    }, 10000);
  }
}
```

然后同样需要在`app.routing.module.ts`文件中配置：

```typescript
const routes: Routes = [
  ...
  {path: 'home', component: HomeComponent, children: [
    {path: '', component: CategoryComponent},
    {path: 'tags', component: TagComponent}
  ], canDeactivate: [UnSaveGuard]},
  ...
];

@NgModule({
  ...
  providers: [LoginGuard, UnSaveGuard]
})
export class AppRoutingModule { }
```

## `Resolve`

`Resolve`可以帮助指定的路由组件在初始化组件之前获得所需的后端数据，可以避免在路由到页面的时候再去获取数据，造成DOM中数据空白的问题，影响用户体验。

我们生成一个获取文章内容的`Resolve`：`ng g class routerGuards/articleResolve`

打开`article-resolve.ts`文件，编辑Resolve守卫：

```typescript
import { Resolve, ActivatedRouteSnapshot, Router } from '@angular/router';
import { Article } from '../article/article.component';
import { Injectable } from '@angular/core';

@Injectable()
export class ArticleResolve implements Resolve<Article> {
    constructor(private router: Router) {}
    resolve(route: ActivatedRouteSnapshot) {
        const id: number = parseInt(route.params['id'], null);
        if (id === 1) {
            return new Article('文章标题', '文章内容1231231231231231231文章内容', new Date());
        } else {
            this.router.navigate(['/home']);
            return undefined;
        }
    }
}
```

- `Resolve`接口需要一个泛型参数，类型是返回的数据对象，这里我是一个`Article`对象，定义在`article.component.ts`文件中：
```typescript
export class Article {
  constructor(
   public title: string,
   public content: string,
   public time: Date) {}
}
```
- `Resolve`方法返回的不是bool类型，而是`any`或者可订阅的流、Promise。


然后在`article.component.ts`文件中编辑接收该对象：

```typescript
export class ArticleComponent implements OnInit {
  /**
   * 依赖注入ActivatedRoute对象，用来获取路由信息
   * @param routerInfo 当前激活路由对象
   */
  constructor(private routerInfo: ActivatedRoute) { }

  article: Article;
  ngOnInit() {
    this.routerInfo.data.subscribe((data: {article: Article}) => {
      console.log(data);
      this.article = data.article;
    });
  }
}
```

- 在`article`组件中，我们在初始化`ngOnInit`函数中接收`Resolve`传递过来的参数值。
- 注意要订阅这个参数需要用到`ActivatedRoute`的`data.subscribe`，而不是`params.subscribe`。
- 而且`subscribe`函数中的匿名函数的参数是一个对象，该对象的属性名必须是路由中配置的名字。

接下来我们配置`app.routing.module.ts`文件中的路由参数，顺便了解上面说的“属性名”：

```typescript
const routes: Routes = [
  ...
  {path: 'article/:id', component: ArticleComponent, resolve: {article: ArticleResolve}},
  ...
];

@NgModule({
  ...
  providers: [LoginGuard, UnSaveGuard, ArticleResolve]
})
export class AppRoutingModule { }
```

- 上面说的参数对象的属性名，就是路由参数配置中`resolve`的值对象的属性名，这个属性名需要和`subscribe`订阅的参数对象属性名保持一致。

这时再打开浏览器访问`http://localhost:4200/article/1`就可以看到数据被展示(具体展示就不演示)出来了。
在这个简单的例子中可能感受不到`Resolve`的作用，但在实际的项目中，使用`Resolve`去后端获取数据，就能展现出来它的作用了。

# 9. 简化版路由绑定方式

说一下简化版的路由绑定，这个免去了`RouterLink`的复杂绑定过程，可以简单的指定一个路由链接，比如：

```html
<a *ngFor="let hero of heroes" class="col-1-4"
    routerLink="/detail/{{hero.id}}">
```

- 可以看到直接使用属性`routerLink`赋值一个字符串+插值表达式的组合，就可以匹配简单规则的路由了。但是遇到复杂要求的路由还是推荐使用`[routerLink]`的方式。

可以在官方文档的[这个](https://www.angular.cn/tutorial/toh-pt5)例子了解简单路由绑定的编写方式。

# 10. 参考资料

[https://www.angular.cn/guide/router](https://www.angular.cn/guide/router)  
[https://www.angular.cn/tutorial/toh-pt5](https://www.angular.cn/tutorial/toh-pt5)