---
title: GO环境安装部署
date: 2017-08-08T21:50:58+08:00
categories: ["Golang"]
tags: ["Golang入门"]
permalink: 
description: GO环境安装部署
photos: http://ww1.sinaimg.cn/large/c55a7aeely1ficnszlhh9j20go098jrl.jpg
---
# Windows安装GO

  官网下载[https://golang.org/dl/](https://golang.org/dl/)
  Golang中国下载[https://golangtc.com/download](https://golangtc.com/download)
直接安装即可。

<!--more-->

# 部署环境

## 查看安装状态

上一步安装完成后，打开命令行工具，输入`go env`回车，就可以看到如下界面：
![](http://ww1.sinaimg.cn/large/c55a7aeely1fico3eyuuqj20jg0ah0te.jpg)
有几个关键的参数：

| 参数             | 解释                                                              |
| :--------------- | :---------------------------------------------------------------- |
| GOARCH=amd64     | 表示目标机器的处理器架构，它的值可以是 386、amd64 或 arm          |
| GOBIN=           | 编译器喝链接器安装位置，默认是`$GOROOT/bin`文件夹，一般不需要设置 |
| GOOS=windows     | 安装GO语言的操作系统                                              |
| GOPATH=E:\GOPATH | GO语言默认的工作目录，可以随意设置(在后面提到)                    |
| GOROOT=G:\GO     | 安装目录                                                          |

## 修改GOPATH

1. 打开环境变量设置：
在系统变量中新建一个环境变量：

| 变量名 | 变量值     |
| :----- | :--------- |
| GOPATH | [你的目录] |
  
2. 修改`path`环境变量：
  在里面新增一个`%GOPATH%\bin`
3. 测试修改
  在命令行输入`go env`查看`GOPATH`是否设置为制定的目录，如果没有变化，关掉命令行重新打开再试试看。

## 查看文档

1. 官方文档：[https://golang.org/](https://golang.org/)
2. 本地文档：
  由于网络的原因，官网可能访问不了，可以构建本地文档，在命令行输入`godoc -http=:8000`,回车之后，在浏览器中输入`localhost:8000`，就可以打开本地的官网了。

## GOPATH规定

  从 Go 1.1 版本开始，GOPATH不能和GOROOT相同文件夹。它可以包含多个包含 Go 语言源码文件、包文件和可执行文件的路径，而这些路径下又必须分别包含三个规定的目录：src、pkg 和 bin，这三个目录分别用于存放源码文件、包文件和可执行文件。
