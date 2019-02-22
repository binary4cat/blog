---
title: 在树莓派3上部署qiandao.today签到程序
date: 2017-03-15T20:41:35+08:00
categories: ["Raspberry"]
tags: ["树莓派应用"]
permalink: Raspberry-pie3-deployed-on-the-qiandao.today-check-in-procedures
description: 在树莓派3上部署qiandao.today签到程序
photos:
---
# 1. 项目介绍
　　[qiandao.today](https://qiandao.today/)是一个在github上开源的自动签到框架，使用python开发，作者是[binux](http://binux.me/)。

　　github:[https://github.com/binux/qiandao](https://github.com/binux/qiandao)
# 2. 部署在本地的树莓派上

## 2.1. 准备
　　首先我们准备一台安装好linux系统(raspbian、ubuntu等)的树莓派(我这里使用的是树莓派3)，连接接显示器并联网，连接键鼠。
<!--more-->
　　操作系统为基于Debian8的Raspbian。并且修改镜像源为国内镜像，然后执行命令更新软件源：`sudo apt-get update`
## 2.2. 部署 
　　签到项目是使用python开发的，被树莓派完美支持，只需安装2.7版本的python即可(如果你的系统是树莓派官方的Raspbian的话，就不需要安装了，系统自带2.7版本的python)。
### 2.2.1. clone项目
　　首先找一个合适的位置，准备存放你的签到项目，我准备放在`Documents`文件夹下面，县使用命令行：`cd /home/pi/Documents`回车进入`Documents`文件下下面：

　　然后使用命令clone项目：`git clone https://github.com/binux/qiandao.git`
![](/image/raspberry/pi-1.png)

　　如果没有执行clone，可能是git没有安装，执行命令：`sudo apt-get install git`  安装完git再执行上面的克隆命令。
### 2.2.2. 安装项目依赖的组件
　　项目我们已经克隆倒本地了，现在我们需要安装运行项目所需要的组件。参照项目中的`README.md`，我们知道需要安装sqlte3数据库，还有好几个python依赖组件。

　　首先我们使用命令安装sqlite3数据库：`sudo apt-get install sqlite3`

　　接下来使用项目`README.md`介绍里面的两条命令安装依赖：
```shell
sudo apt-get install python-dev autoconf g++ python-pbkdf2
sudo pip install tornado u-msgpack-python jinja2 chardet requests pbkdf2 pycrypto
```

PS：如果安装失败，很可能是网络原因，需要改用国内镜像安装，这里我们使用[豆瓣源](https://www.douban.com/note/302711300/)安装：在命令`install`后面加上`-i http://pypi.douban.com/simple/` 即可，例如：
```shell
sudo pip install -i http://pypi.douban.com/simple/ tornado u-msgpack-python jinja2 chardet requests pbkdf2 pycrypto
```

## 2.3. 启动
　　至此我们的准备工作都已经做完了，是时候启动项目了。

　　首先进入刚才clone的项目文件夹：`cd /home/pi/Documents/qiandao`

　　然后执行命令：`./run.py`

　　回车后我们就可以看到项目已经启动了，窗口显示了请求地址以及端口号：
![](/image/raspberry/pi-2.png)

　　这是我们已经可以在浏览器访问了，在树莓派里面打开浏览器，输入`127.0.0.1:8923`然后回车访问，就可以直接浏览了：
![](/image/raspberry/pi-3.png)

# 3. 结束
　　至此[qiandao.today](https://qiandao.today/)项目已经部署到树莓派上面了，如果我们想在局域网内的其他设备上访问，可以在树莓派里面使用命令：`ifconfig`查看树莓派的ip地址，然后在其他设备输入`IP:8923`，就可以访问啦。如果更近一步，我们想使用外网访问，那么可以使用花生壳做外网映射，就可以在任何设备上访问你部署在树莓派上的签到网站了，关于树莓派花生壳外网映射，可以参照这篇文章：[花生壳 3.0 for 树莓派 安装使用攻略](http://service.oray.com/question/2680.html)。

　　这篇文章写到这里就结束了，如果大家在部署的时候遇到问题，可以在“关于页面找到我的联系方式，希望我能帮到你。”
