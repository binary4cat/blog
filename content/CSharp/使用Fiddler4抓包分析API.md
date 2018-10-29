---
title: 使用Fiddler抓包分析API
date: 2016-07-17T13:22:44+08:00
tags: ["UWP学习笔记"]
categories: ["cSharp"]
permalink: Fiddler-caught-analysis-API
description: 使用Fiddler抓包分析API
---
> Fiddler是一个http协议调试代理工具，它能够记录并且检查所有你的电脑和互联网之间的http通讯，设置断点，查看所有的“进出”Fiddler的数据（指cookie,html,js,css等文件，这些都可以让你胡乱修改的意思）。 Fiddler 要比其他的网络调试器要更加简单，因为它不仅仅暴露http通讯还提供了一个用户友好的格式。Fiddler 是用C#写出来的,它包含一个简单却功能强大的基于JScript .NET 事件脚本子系统，它的灵活性非常棒，可以支持众多的http调试任务，并且能够使用.net框架语言进行扩展。

　　摘录一下百科的解释，简单点说Fiddler是个抓包工具，测试人员可能用的比较多，在UWP开发中使用的，就是为了开发第三方的软件而进行分析API接口，至于为啥要开发第三方的UWP，这得把巨硬抓起来打一顿就知道了。
　　
## 准备
　　需要的工具：Android手机，电脑，WiFi。
　　首先我们去官网([www.telerik.com](https://www.telerik.com/download/fiddler))下载并安装Fiddler软件。<!--more-->

## 设置Fiddler
　　**设置HTTPS代理和允许客户端/服务端代理**
　　打开软件，依次选择Tools>Fiddler Options...打开该选项卡。
　　进入*HTTPS*选项，勾选下列项：
![](http://ww3.sinaimg.cn/mw690/c55a7aeejw1f5wvo3emwej20f10a7glv.jpg)
　　进入*connections*选项，勾选下列项，并填写端口号(默认为8888)：
![](http://ww4.sinaimg.cn/mw690/c55a7aeejw1f5wwxb99qaj20f60af74m.jpg)
　　因为我们现在需要抓包手机，所以为了避免电脑端通讯的干扰，我们要检查一下电脑是否被Fiddler代理，如果是的话就关闭该代理。
![](http://ww2.sinaimg.cn/mw690/c55a7aeejw1f5wx0q0nisj20px0gp0tb.jpg)

## 设置手机端
　　**首先，保证手机和PC处于同一局域网中(PC分享WiFi给手机，或者笔记本和手机连接同一个WiFi)。**
　　1. 使用命令行(ipconfig)查看电脑的IP地址。(我这里使用的笔记本分享的网络，选择了手机连接的这个网络的IP地址)
![](http://ww2.sinaimg.cn/mw690/c55a7aeejw1f5wy9i9wncj20r60ckgmr.jpg)
　　2. 手机连接该WiFi，然后在高级设置中，配置HTTP代理。
![](http://ww3.sinaimg.cn/mw690/c55a7aeejw1f5wy8pts28j20u01hcthj.jpg)

## 开始抓包分析数据
　　接下来，我们以网易公开课App为例，说明一下如何分析接口数据。

　　1. 打开网易公开课App，然后观察Fiddler软件抓取到的数据。
　　2. 对比App和Fiddler中的数据。

　　
