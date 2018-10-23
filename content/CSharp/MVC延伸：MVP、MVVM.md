---
title: MVC延伸：MVP、MVVM
date: 2015-09-19T01:16:16+08:00
tags: ["Asp.Net MVC"]
categories: ["CSharp"]
permalink: Extends-MVC-MVP-and-MVVM
description: MVC延伸：MVP、MVVM
---
　　学习和使用ASP.NET MVC也有一段时间了，对这种开发方式(模式)有了更进一步的了解，今天就跳出ASP.NET的圈子，说一说我了解到的MVC，以及延伸到的MVP、MVVM。(如果理解的不到位希望有看到的这篇博文的同学在评论中指出😛)

## MVC
　　一开始接触MVC时最先想到的是，MVC算不算是一种设计模式？为什么GoF的23种设计模式中没有MVC模式？ <!--more-->
　　这个问题困扰了我很长时间，常常疑惑MVC为什么不是一种设计模式。之后看了大漠老师的一片博文[《为什么MVC不是一种设计模式？ ---比较Backbone和Ext4.x在MVC实现上的差异》](http://damoqiongqiu.iteye.com/blog/1949256)，反复读了几遍，感觉获益颇深。
　　今天我们暂时不讨论这个问题，我的啰嗦就此作罢，进入正题。😛😛😛

　　按照惯例，上图！👀
![](http://ww2.sinaimg.cn/mw690/c55a7aeejw1f1d510o2nfj20dt08ht8y.jpg)
　　如图中所示的，M、V、C三个组件都是单向联系的关系。
　　■ View(V):首先View在交互层面被触发，将信息传递给Controller(C)。
　　■ Controller(C):Controller收到了来自View的交互请求，判断该请求需要执行的操作和获取的数据。然后将View所需的数据信息发送给Model(M)。
　　■ Model(M):Model接收到了请求数据的消息，然后组织所需的数据，交给View展示。
　　上面所述的算是MVC的一个完整的执行流程，可能在不同的技术栈中实现是不相同的，但是大致上架构思路却也相同。

## MVP
![](http://ww1.sinaimg.cn/mw690/c55a7aeejw1f17gi6rfa9j20e308yaac.jpg)
　　作为MVC的延伸，MVP取消掉了View(V)和Model(M)之间的联系，并且将单向的关系变成了双向。
　　■ 将Controller(C)变成了Presenter(P)，并且View(V)的变化由Presenter负责，因为它们之间是双向关系。
　　■ Presenter(P)负责了主要的业务逻辑，View和Model都由它来负责了。

## MVVM
![](http://ww2.sinaimg.cn/mw690/c55a7aeejw1f17gunrixcj20dp090t8z.jpg)
　　与MVP很相似，切断了Model(M)和View(V)之间的联系；比MVP更有趣的地方在于，ViewModel(VM)和View(V)之间是双向绑定的关系，也就是只要一方更新，另一方会自动的接受信息并相应的更新。如果学过WPF或者AngularJS这种体会会更深刻。
　　■ ViewModel和View之间双向绑定，一方的变动会自动的反应在另一方。

## 总结
　　① MVC模式中，有一部分的交互逻辑可能会出现在View层，造成View太厚，所以MVP的出现就是要将主要的代码逻辑放在Presenter中，让View只做展示。
　　② MVC模式有两种可能的交互方式：第一种，直接在View层交互，比如用户点击按钮等触发操作；第二种就是直接触发Controller，比如直接修改输入URL连接。
　　③ 在MVP和MVVM模式中，P或者VM包揽了大部分的交互逻辑，View只负责展示数据。
　　④ 三种模式的优点在于将关注点分离，每一个组件都只负责自己的事情，比如Controller、Persenter、ViewModel只负责业务逻辑的处理，它不用去管Model是怎么处理数据的、View是怎么展示的，甚至并不知道这两者的存在。
　　⑤ 在ASP.NET MVC中View负责了一部分的展示逻辑处理，使用`HtmlHelper`处理一些交互逻辑，但是在一个大项目中，可能对前端人员造成困扰，它们必须要能看懂这种特殊的语法。这种情况在MVC6中得到了改善，新的`HtmlHelper`语法更符合HTML标签的语义，让前端人员可以“猜”出来代码所表示的交互逻辑。
