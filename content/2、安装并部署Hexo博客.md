---
title: 2、安装并部署Hexo博客
date: 2016-03-05T20:46:11+08:00
tags: ["Hexo博客搭建"]
categories: ["Hexo"]
permalink: Hexo-Getting-Started-2
description: Hexo博客搭建
---
　　下面的搭建过程是在Windows系统上进行的，Linux系统和OSX系统可以看[官方文档](https://hexo.io/zh-cn/docs/index.html)。

## 安装
　　因为Hexo是基于nodejs编写的，而且使用Git进行管理，所以需要安装这两个工具。
**安装Git工具**
　　直接百度Git的Windows安装包或者去[官网下载](http://git-scm.com/download/)安装，安装过程中无需特殊设置，一路next即可。

**安装NodeJs**
　　nodejs也可以直接去[中文网](http://nodejs.cn/)或[英文官网](http://nodejs.org/)下载。nodejs有两个版本：LTS(长期支持版)、Stable(稳定版)。建议大家下载LTS长期支持版。
<!--more-->
**安装Hexo**
　　将Hexo安装到电脑中，这样就可以在全局使用`hexo`命令进行操作了。
　　在任意地方鼠标右击，选择“Git Bash Here”。(一般Git工具安装成功的话右键菜单都会有这个选项，如果没有的话请查看电脑已安装的软件中有没有Git，如果没有重装或者重启电脑试试看。)
![](http://ww1.sinaimg.cn/mw690/c55a7aeejw1f1map7rgv4j209y0e175a.jpg)
　　在命令行窗口中输入命令然后回车：
```
npm install -g hexo-cli
```

## 创建并初始化Hexo博客
**创建Hexo博客文件夹**
　　在任何合适的文件夹下面(比如我的创建在E:\Is2Blog)，在该文件夹中鼠标右击，依旧选择“Git Bash Here”打开命令行窗口。
　　输入初始化命令：
```
hexo init
```
　　完成后应该可以看到多了几个文件夹。![](http://ww2.sinaimg.cn/mw690/c55a7aeejw1f1mbhjwda1j20vy0h6q45.jpg)

**安装部署工具**
```
npm install hexo-deployer-git --save
```
　　这一步是安装部署博客的组件，在后面部署博客时遇到的大多数错误都是由于没有安装这个组件，所以避免后面出错，我将这个组件的安装操作提前到了最开始。

**本地安装完成**
　　到目前为止我们已经在本地创建好了Hexo博客，并且已经可以在本地访问了：
　　在刚刚创建Hexo的文件夹下使用“Git Bash Here”打开命令行窗口，分别输入下面的两行命令：
```
hexo generate
hexo server
```
　　这两条命令的作用分别是生成博客静态文件、启动预览服务。
　　这时可以看到窗口中提示我们访问`http://localhost:4000`了，现在复制这个地址在浏览器中打开试试。
![](http://ww2.sinaimg.cn/mw690/c55a7aeejw1f1memdmu7fj212n0m1wn6.jpg)


## 将本地博客部署到github上
　　现在这个博客还只能在本地访问，因为我们还没有部署到服务器上面。
　　Hexo可以部署至云服务器或其他类型的服务器，这里我们只是简单的技术博客，可以使用github免费提供的pages服务，托管我们的博客。
 
**注册github**
　　可以登录[https://github.com/](https://github.com/)注册一个账号。

**创建pages项目**
　　注册成功后，登录到主页，然后创建一个用来托管博客的pages页面项目：
![](http://ww1.sinaimg.cn/mw690/c55a7aeejw1f1mc2nd05dj20ul08wac4.jpg)
![](http://ww2.sinaimg.cn/mw690/c55a7aeejw1f5i3vr82tpj20qs0h5q3y.jpg)

　　创建完成之后，github项目库应该是这种形式的：![](http://ww3.sinaimg.cn/mw690/c55a7aeejw1f1mcjap8y1j20d202et8s.jpg)

**将本地博客部署到github**
　　现在我们可以将本地的Hexo博客部署到刚才创建的库中，以便能够通过网络访问我们的博客。
　　部署的操作，之后会使用hexo命令行进行部署，现在我们先配置提交方式和地址：
　　在我们创建的博客文件夹下找到`_config.yml`文件，使用编辑器打开，在文件最后输入下面内容：
```
deploy:
  type: git
  repository: https://github.com/username/username.github.io.git
  branch: master
```
　　在添加上面的配置时请注意：
　　1. 每一个冒号(:)之后应该有一个半角空格，否则无法拿到后面的值。
　　2. `repository`的值是你的项目的地址，可以在你的项目中找到，比如：![](http://ww1.sinaimg.cn/mw690/c55a7aeejw1f1mcvvqn27j20s003fglp.jpg)

　　上面的配置完成之后，现在可以将博客文件提交至github库中了，在“Git Bash Here”命令行窗口中分别输入下面的命令：
```
hexo generate
hexo deploy
```
　　上面的命令可以使用简写形式缩短至一行：`hexo d -g` ，也就是生成并部署。

　　PS：在部署提交的时候，可能会提示你输入github的账号和密码，输入即可，在之后会介绍使用SSH密钥的方式提交，免去每次提交都要输入账号密码的麻烦。

**通过网址访问**
　　现在博客已经部署到github中了，由github托管，已经可以通过链接的形式访问了，可以在浏览器中输入你创建的库的名称，比如我的是：haijiandong.github.io
　　将你的地址输入到浏览器试试，如果碰到404的情况，有两种解决办法：
　　1. 等待一会儿再访问，可能是网络有延迟。
　　2. 打开你注册时填写的邮箱，看看是否有github发过来的邮件(注意垃圾箱)，如果有的话打开看看，很多时候都是没有注意到github的邮件造成404错误。
