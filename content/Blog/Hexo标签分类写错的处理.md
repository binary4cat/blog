---
title: Hexo标签分类写错的处理
date: 2016-07-18T01:17:13+08:00
tags: ["Hexo博客搭建"]
categories: ["Blog"]
permalink: Hexo-misspelled-tag
description: Hexo标签分类写错的处理
---
　　有时候我们会不小心写错了Hexo的标签，部署之后需要改正的话发现改完之后，之前的那个错误的标签或者分类的名字依然存在，这时我们需要删除本地的部署文件以及生成文件的json数据。

　　打开Hexo博客，删除根目录下的**db.json**文件和**.deploy_git**文件夹。<!--more-->
　　在博客目录下打开*Git Bash*,运行下列命令
```
hexo clean
hexo d -g
```
　　*第一条命令是清除Hexo缓存，第二条是生成以及部署博客。*

　　执行完以上操作，错误的标签分类名称就会被彻底清除掉了。
