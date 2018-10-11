---
title: "Angular知识点汇总(1)：起步和项目结构介绍"
date: 2018-10-10T22:13:31+08:00
tags: ["Angular"]
categories: ["Frontend"]
description : "Angular知识点汇总(1)：起步和项目结构介绍"
draft: false
---
<!-- TOC -->

- [1. 起步](#1-起步)
    - [1.1. 安装angular-cli](#11-安装angular-cli)
    - [1.2. 生成项目](#12-生成项目)
    - [1.3. 尝试运行生成的项目](#13-尝试运行生成的项目)
- [2. 项目架构](#2-项目架构)
    - [2.1. 文件结构](#21-文件结构)
    - [2.2. 组件](#22-组件)
        - [2.2.1. 组件](#221-组件)
            - [2.2.1.1. angular项目的启动过程](#2211-angular项目的启动过程)
            - [2.2.1.2. 根模块AppModule的内容](#2212-根模块appmodule的内容)
        - [2.2.2. 组件装饰器](#222-组件装饰器)
- [基本的项目开发](#基本的项目开发)
    - [生成一个自定义的组件](#生成一个自定义的组件)
    - [怎么引入第三方的库](#怎么引入第三方的库)
    - [Hello World](#hello-world)
- [3. 参考资料](#3-参考资料)

<!-- /TOC -->

# 1. 起步

## 1.1. 安装angular-cli

首次学习的同学可以进入到[angular-cli wiki页面](https://github.com/angular/angular-cli/wiki)，学习关于angular-cli的知识。
使用npm安装angular-cli(也可以使用cnpm进行安装，避免网络问题造成失败)：

```shell
npm install -g @angular/cli
```

或者可以使用[yarn](https://yarnpkg.com/zh-Hans/)安装，同样可以避免网络问题：

```shell
yarn add golbal @angular/cli
```

## 1.2. 生成项目

找一个合适的文件夹，在文件中打开命令行工具，执行生成命令，angular-cli会帮我们生成一个基础项目，避免从0开始搭建的麻烦：

```shell
ng new my-project
cd my-project
```

这里可能会因为网络的问题造成`ng new my-project`命令执行失败，造成生成的基架项目无法运行，可以将`ng`命令调用的包管理命令改成其他的工具，例如`cnpm`或者`yarn`：

- 首先打开`Home_Dir/.angular-config.json`文件
- 然后修改其中的内容为需要的工具：

```json
{
  "version": 1,
  "cli": {
    "packageManager": "yarn"
  }
}
```

- 然后再执行`ng new my-project`就不会因为网络问题失败了。

## 1.3. 尝试运行生成的项目

在项目文件夹下执行下面的命令：

```shell
ng serve
```

在浏览器中运行[http://localhost:4200](http://localhost:4200)，即可看到运行生成的项目。

# 2. 项目架构

## 2.1. 文件结构

![文件目录](/image/Snipaste_2018-10-10_22-59-26.png)

- `e2e`文件夹中是e2e端到端测试需要的文件。
- `node_modules`文件夹中是项目中所需的npm包。
- `src`文件夹中是项目主要的文件：
    - `app`文件夹中存放项目中的所有组件，默认生成的项目中会有根组件的几个文件。
    - `assets`文件夹中是项目中所需的静态文件的存放目录。
    - `environments`文件夹中存放着项目所需的配置文件，具体配置方式会在后面的文章讲到。
    - `index.html`是整个项目的根模板，所有的页面都是在这个页面的基础上的。
    - `karma.conf.js`是自动化测试karma框架的配置文件。
    - `main.ts`是项目的起始文件，项目的启动由此启动。
    - `polyfills.ts`该文件导入一些解决浏览器兼容性的包。
    - `tsconfig.app.json`/`tsconfig.spec.json`文件是typescript的配置文件，该文件的配置extends上级目录的`tsconfig.json`配置。
    - `tsline.json`是typescript规范约束的配置文件，该文件的配置extends上级目录的`tsline.json`配置。
- `angular.json`是当前angular项目的一些配置，例如项目的起始文件、编译目录、环境配置等。
- `package.json`是当前项目所用的包，脚本等配置。
- `tsconfig.json`是typescript的配置文件。
- `tsline.json`是typescript的语法规范约束文件。

## 2.2. 组件

angular项目的核心就是组件，可以说angular项目就是一个一个组件组成的；

### 2.2.1. 组件

#### 2.2.1.1. angular项目的启动过程

使用`ng new`生成的项目，在`app`文件夹中已经包含了一个默认的组件，就是整个项目的根组件，是其他所有我们自定义组件的父组件，因为angular项目在启动的时候，需要从一个根组件开始进行渲染，我们借助`main.ts`文件可以看到angular项目的启动过程，并且看到根组件的调用：

```typescript
import { enableProdMode } from '@angular/core';
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';

import { AppModule } from './app/app.module';
import { environment } from './environments/environment';

if (environment.production) {
  enableProdMode();
}

platformBrowserDynamic().bootstrapModule(AppModule)
  .catch(err => console.error(err));
```

- 首先import了四个对象：
    - `enableProdMode`是让项目使用生产环境模式运行的一个函数。
    - `platformBrowserDynamic`是浏览器模块中的组件，作用是让angular项目在浏览器中运行。
    - `AppModule`这个是默认生成的根模块。
    - `environment`是项目的环境配置，用来判断当前的项目是以开发/测试/生产那个环境运行的。
- if判断当前启动的模式，如果是以生产环境启动，则需要调用`enableProdMode`函数。
- 然后调用`platformBrowserDynamic`函数的初始化模块方法`bootstrapModule`，传入根模块`AppModule`，从根模块开始渲染angular项目。

#### 2.2.1.2. 根模块AppModule的内容

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

- `@NgModule`装饰器接收一些配置：
    - `import`导入当前angular项目运行需要的一些外部模块。
    - `provides`是angular的依赖注入提供器，写在这里面的class，就可以在angular项目的任何构造函数中依赖注入了。
    - `bootstrap`传入了初始化的第一个组件，也就是我们的根组件，其他的组件应该包含在这个根组件下面

### 2.2.2. 组件装饰器


组件装饰器可以修饰一个普通的typescript类，angular会将被修饰的类当作一个组件渲染。

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'my-project';
}
```

- `selector`：选择器，指定当前的组件是通过模板中哪个HTML标签调用的；例如可以在调用该组件的位置写上标签`<app-root><?app-root`，就会在此位置渲染该组件的内容了。
- `templateUrl`：当前的组件模板的文件路径，在渲染的时候会将该文件中的HTML替换到`selector`选择器标签的位置。
- `styleUrls`：当前组件模板用到的样式文件，可以是多个。

# 基本的项目开发

## 生成一个自定义的组件

可以使用命令`ng g component [name]`生成一个自定义的命令，在这里我使用`ng g component test`生成一个`testComponent`:

```shell
PS E:\Angular\my-project> ng g component test
CREATE src/app/test/test.component.html (23 bytes)
CREATE src/app/test/test.component.spec.ts (614 bytes)
CREATE src/app/test/test.component.ts (261 bytes)
CREATE src/app/test/test.component.css (0 bytes)
UPDATE src/app/app.module.ts (388 bytes)
```

然后会在`app`目录下生成一个名为`test`的文件夹，其中包含组件的四个文件：
![自定义组件test]](/image/Snipaste_2018-10-11_22-24-40.png)

- `test.component.css`该文件是当前组件的样式文件；
- `test.component.html`该文件是当前组件的模板文件；
- `test.component.spec.ts`该文件是组件源文件的单元测试，当使用`ng test`命令的时候，这个文件会通过Karma任务运行器使用Jasmine javascript测试框架运行。
- `test.component.ts`该文件是组件的“控制器”代码文件。


## 怎么引入第三方的库

有时候我们需要在项目中用到一些第三方的库，例如样式库bootstrap、搭配bootstrap使用的Jquery等，这些库需要单独的引入angular项目，才可以使用。在这里我使用yarn安装一下这两个库：

```shell
cd my-project
yarn add bootstrap
yarn add jquery
```

安装完成后打开`angular.json`文件，找到`styles`和`scripts`节点，把需要用到的bootstrap和jQuery文件导入项目：

```json
{
  ...
  "projects": {
    "my-project": {
      "architect": {
        "build": {
          "options": {
            ...
            "styles": [
              "src/styles.css",
              "node_modules/bootstrap/dist/css/bootstrap.css"
            ],
            "scripts": [
              "node_modules/bootstrap/dist/js/bootstrap.js",
              "node_modules/jquery/dist/jquery.js"
            ]
            ...
          },
        },
      }
    },
  ...
}
```
然后就可以在项目中使用bootstrap库了。

接下来打开`test.component.ts`看一下组件的基本结构：

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-test',
  templateUrl: './test.component.html',
  styleUrls: ['./test.component.css']
})
export class TestComponent implements OnInit {

  constructor() { }

  ngOnInit() {
  }

}
```

- 首先从angular的核心库中导入了`Component`这个装饰器和初始化生命周期接口`OnInit`。
- 要使用初始化方法`ngOnInit`需要组件类实现`OnInit`接口。其他的生命周期方法也需要实现各自的接口，在后面会讲到。
- `constructor`构造函数可以用来依赖注入一些服务，避免手动创建类，方便解耦。

## Hello World

使用一个简单的示例，演示编写组件的工作流程：

`test.component.ts`：

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-test',
  templateUrl: './test.component.html',
  styleUrls: ['./test.component.css']
})
export class TestComponent implements OnInit {
  /**
   * 声明一个消息属性
   */
  msg: string;

  constructor() { }

  ngOnInit() {
    /**
     * 在初始化时，给消息属性赋值
     */
    this.msg = 'Hello World';
  }
}
```

`test.component.html`：使用绑定语法`{{}}`将消息展示在页面上

```html
<h1 class="display-4 text">{{msg}}</h1>
```

`test.component.css`：将展示的字体修改成白色

```css
.text{
    color: white;
}
```

`app.component.html`：将自定义组件test绑定到根组件`app`的模板中

```html
<div class="jumbotron">
  <h1 class="display-4">我是根组件</h1>
  <p class="lead">这是一个简单的bootstrap Jumbotron样式组件！</p>
  <hr class="my-4">
  <p>下面绑定的是子组件的内容：</p>
  <p class="lead">
    <app-test></app-test>
  </p>
</div>
```

使用命令`ng serve`启动服务，并在浏览器浏览：
![浏览内容](/image/Snipaste_2018-10-11_23-13-09.png)

# 3. 参考资料

[https://github.com/angular/angular-cli/wiki](https://github.com/angular/angular-cli/wiki)  
[https://medium.com/@beeman/using-yarn-with-angular-cli-v6-7f53a7678b93](https://medium.com/@beeman/using-yarn-with-angular-cli-v6-7f53a7678b93)