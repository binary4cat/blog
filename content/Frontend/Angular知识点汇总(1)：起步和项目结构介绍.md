---
title: "Angular知识点汇总(1)：起步和项目结构介绍"
date: 2018-10-10T22:13:31+08:00
tags: ["Angular"]
categories: ["Frontend"]
description : "Angular知识点汇总(1)：起步和项目结构介绍"
draft: false
---
<!-- TOC -->

- [1. 起步](#1-%E8%B5%B7%E6%AD%A5)
    - [1.1. 安装angular-cli](#11-%E5%AE%89%E8%A3%85angular-cli)
    - [1.2. 生成项目](#12-%E7%94%9F%E6%88%90%E9%A1%B9%E7%9B%AE)
    - [1.3. 尝试运行生成的项目](#13-%E5%B0%9D%E8%AF%95%E8%BF%90%E8%A1%8C%E7%94%9F%E6%88%90%E7%9A%84%E9%A1%B9%E7%9B%AE)
- [2. 项目架构](#2-%E9%A1%B9%E7%9B%AE%E6%9E%B6%E6%9E%84)
    - [2.1. 文件结构](#21-%E6%96%87%E4%BB%B6%E7%BB%93%E6%9E%84)
    - [2.2. 组件](#22-%E7%BB%84%E4%BB%B6)
        - [2.2.1. 组件](#221-%E7%BB%84%E4%BB%B6)
            - [2.2.1.1. angular项目的激动过程](#2211-angular%E9%A1%B9%E7%9B%AE%E7%9A%84%E6%BF%80%E5%8A%A8%E8%BF%87%E7%A8%8B)
            - [2.2.1.2. 根模块AppModule的内容](#2212-%E6%A0%B9%E6%A8%A1%E5%9D%97appmodule%E7%9A%84%E5%86%85%E5%AE%B9)
    - [2.3. 两个装饰器](#23-%E4%B8%A4%E4%B8%AA%E8%A3%85%E9%A5%B0%E5%99%A8)
        - [2.3.1. `@Component`](#231-component)
        - [2.3.2. `@NgModule`](#232-ngmodule)
- [3. 参考资料](#3-%E5%8F%82%E8%80%83%E8%B5%84%E6%96%99)

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

#### 2.2.1.1. angular项目的激动过程

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

## 2.3. 两个装饰器

### 2.3.1. `@Component`

### 2.3.2. `@NgModule`

# 3. 参考资料

[https://github.com/angular/angular-cli/wiki](https://github.com/angular/angular-cli/wiki)  
[https://medium.com/@beeman/using-yarn-with-angular-cli-v6-7f53a7678b93](https://medium.com/@beeman/using-yarn-with-angular-cli-v6-7f53a7678b93)