---
title: 理解RESTful
date: 2016-02-24T23:37:09+08:00
tags: ["ASP.NET Web API笔记"]
categories: ["cSharp"]
permalink: Understanding-RESTful
description: 理解RESTful
---
　　在学习Web API之前，我们首先接触的一个概念就是RESTful，个人觉得从名字(英文全名)上来理解还是很抽象的一个概念，刚开始接触的时候感觉似懂非懂，但是使用一段时间后再回来看这个问题就会比较清晰了。
　　在这篇博文中，我尽力把我所理解的RESTful解释清楚，理解错误可能难免，希望有看到的同学能指点一下。

😒 **简单粗暴版**：<!--more-->
　　RESTful是一种软件架构风格、设计风格，而不是标准或者实现。
　　想想咱们操作网站，其实就是把人家网站服务器的资源增删改查，所以大神就想了个好办法，正好HTTP协议里面有几个状态能配对儿，程序员也用这方法写网站就行了啊！所以RESTful就是大神提出来的解决方案，利用HTTP协议中的几个动词：*GET*(获取资源)、*POST*(新建资源)、*PUT*(更新资源)、*DELETE*(删除资源)直接对服务器后台发送命令，这样既简单又符合规矩。

# 1. 正文
　　REST的提出者是HTTP协议的主要设计者、Apache基金会的第一任主席*Roy Fielding*，在2000年时他的博士论文[《Architectural Styles and the Design of Network-based Software Architectures》](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)中首次提到了REST这个词。
　　REST的全拼是**Representational State Transfer**，翻译成中文就是**表征状态转移**。

　　首先我们思考一下我们在网络上进行各种交互，其实就是对网络上的各种资源进行操作，这里的资源指向的就是网络上的一个存在的实体，比如一首歌、一部电影、一张图片、一片文章等等，每一个资源都可以使用URI(统一资源定位符)进行对应，通过访问相应的URI就可以拿到想要的资源。操作网络就是操作URI对资源进行操作。
　　表征状态转移这个词的含义就是，通过操作对资源的状态进行改变。
　　客户端想要对服务端的资源状态进行操作，必须以一种手段让服务端的资源状态发生改变(比如修改添加等)。那么使用什么手段才能让客户端改变服务端的资源状态呢？只能是HTTP协议了，所以就使用HTTP协议中的4个表示操作的动词：GET、POST、PUT、DELETE。这几个词在REST架构中分别对应几种功能的操作：
　　● GET:用来获取资源
　　● POST:用来新建资源，也可以更新资源
　　● PUT:用来更新资源
　　● DELETE:用来删除资源

　　所以我们总结一句话说明什么是RESTful：客户端使用HTTP协议中表示操作的4个动词，通过URI的形式对服务端的资源进行改变(操作)。👽

　　💋 ps:当然作为一种架构风格，从2000年开始讨论至今，其实现原理等估计一本书都写不完，以上只是为了能理解RESTful的概念，进而对学习使用 ASP.NET WebAPI有所帮助，仅此而已。
