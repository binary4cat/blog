title: 1、Umbraco简介和安装
date: 2016-01-01T21:23:04+08:00
tags: Umbraco入门
categories: Umbraco
permalink: Umbraco-Getting-Started-1
description: Umbraco入门教程
---
　　Umbraco是国外一个开源的Cms(内容管理)系统，适合做新闻发布、官网等内容需要经常更改变动的网站，也可以作为论坛软件使用，比如asp.net论坛就是基于umbraco制作的。而且Umbraco是使用“所见即所得”的形式，对网站的控制更加方便简单。可谓分分钟做出一个功能齐全的网站的节奏。
<!--more-->
　　Umbraco在中国的使用范围并不广，能够找到的中文资料零零散散的，而且很多都是以前的，要知道Umbraco从7.0版本以后就开始使用MVC编写了，之前的webforms已经是过去式了。在刚开始学习使用的时候走了很多弯路，由于没有搞懂Cms系统的开发模式，浪费了很多时间，另一个是英语水平限制，阅读英文文档有很多不理解(自我批评一下，程序员应该好！好！学！英！语！)。想到可能以后应该少有机会再用Umbraco了，所以把学习到的记录下来，以后用到了还能回来看看，而且工作中有用到的朋友也可以参考一下，如果能帮到别人，这是我最大的愿望。

　　
***
### 1、安装
#### 两种安装方法：
　　1、Umbraco的安装比较简单，可以直接去[our.umbraco.org](http://our.umbraco.org)网站进行下载完整的代码压缩文件并且解压到任意合适位置，其次还需要下载微软的一个web开发工具[**WebMatrix**](http://www.microsoft.com/web/webmatrix/),安装之后可以使用这个工具打开并且编辑Umbraco了。　
　　2、打开VS建立一个新的空web程序，然后打开**程序包管理控制台**，在命令行界面输入`Install-Package UmbracoCms`这个命令安装Umbraco；之后会把Umbraco以NuGet包的形式安装在当前的空web程序当中，推荐这种安装方法，因为如果有升级之类的，使用NuGet比直接下载方便管理一些。
　　
　　接下来运行Umbraco程序，会进入一个安装的页面，在这里需要进行后台账号密码、数据库类型、是否需要实例开始包安装等操作。

　　下图是首次进入Umbraco的设置界面，需要设置用户名(Name)、登陆umbraco时使用的邮箱(Email)、登陆密码(Password)。下面两个按钮分别是直接安装和进行其他的设置，这里我们点击**Customize**。
　　![](/image/umbraco/setting1.png)
　　点击Customize进入选择数据库类型的界面，如下图所示，有多种选择，Umbraco支持内嵌的CE数据库，MySql、SqlServer以及微软自家的Azure云。选择好数据库之后，填写数据库名称、数据库登陆账号密码等信息后，Umbraco会自动创建连接字符串；这里我们示例使用CE数据库。
　　![](/image/umbraco/setting2.png)
　　点击continue进入下一步操作，这里会让你选择一个官方的示例开始包(starterwebsite)，里面包含了一个简单的Umbraco网站模板，是为了让你更快上手Umbraco的使用。在这里我们点击**No thanks, I do not want to install a starter website**就不使用官方的模板了，因为我们接下来需要自己创建一个网站模板。
　　![](/image/umbraco/setting3.png)
　　接下来，我们等待安装完成，进入Umbraco的后台管理界面。如下图：
　　![](/image/umbraco/backoffice1.png)
　　至此，我们就已经成功的安装了Umbraco，接下来将介绍Umbraco后台管理的一些详细功能。
　　