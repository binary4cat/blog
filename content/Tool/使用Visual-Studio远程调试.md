---
title: 使用Visual Studio远程调试
date: 2017-04-12T21:18:28+08:00
categories: ["Tool"]
tags: ["Visual Studio"]
permalink: Using-the-Visual-Studio-Remote-debugging
description: Using the Visual Studio Remote debugging
photos: http://ww1.sinaimg.cn/large/c55a7aeely1ficp8vja6yj20zk0k0wen.jpg
---
> 本篇记录使用本地Visual Studio对远程服务器上部署的web站点进行断点调试。

# 1. 准备工作

1. 复制vs远程调试文件到服务器c盘，文件夹路径：`Common7/IDE/Remote Debugger/x64` (整个文件夹复制进去)
 2. 运行其中的`msvsmon.exe`可执行程序
 <!--more-->
 3. 选择 *工具-->选项*，修改成 无身份验证，允许任何用户进行调试
 4. VS中选择 *调试-->附加到进程* :
    - 传输：远程(无身份验证)
    - 限定符：xxx.xxx.x.xx:4020
    - 勾选显示所有用户进程
    - 刷新，选择`w3wp.exe`，最后点击附加

 # 关于无法命中的可能性处理
 1. 如果断点无法命中，复制本地项目文件夹 `bin`文件夹中的`pdb`文件到服务器发布项目文件夹的`bin`文件夹中。
 2. 确保服务器上部署的跟本地是同一版本。
