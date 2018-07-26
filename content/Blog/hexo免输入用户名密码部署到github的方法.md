---
title: hexo免输入用户名密码部署到github的方法
date: 2016-01-22T23:34:58+08:00
tags: ["Hexo博客搭建"]
categories: ["Blog"]
permalink: Hexo-Getting-Started-9
description: hexo免输入用户名密码部署到github的方法
---
　　最近一直在写博客和修改主题样式，因为需要即时发布，所以没有使用`hexo s`本地预览,需要反复的执行`hexo d -g`来发布预览效果，但是每次都需要输入*username*和*password*，非常麻烦，想到在使用git时可以设置SSH来免输入账号密码，所以试了试，但是好像没什么用，所以google了一下，找到了解决办法。
<!--more-->
　　实现方法比较简单，只需要设置环境变量和_netrc文件就可以了。
　　首先设置环境变量：`变量名：HOME`，`变量值：%USERPROFILE%`
![](/image/hexo/hexo1.png)
![](/image/hexo/hexo2.png)
![](/image/hexo/hexo3.png)

　　然后在个人文件夹(例如：`C:\Users\MyFile`)下新建名为“**_netrc**”的文件。
　　最后编辑刚刚创建的文件，文件内容如下：
```
machine github.com
login github-username  
password github-password
```
　　第一行保持不变，第二行和第三行分别是你的github用户名和密码。

　　设置完成，以后执行`hexo deploy`就不需要输入用户名和密码啦。
