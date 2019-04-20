---
title: "Win10+WSL配置Golang开发环境"
date: 2019-04-20T22:08:09+08:00
categories: ["Golang"]
tags: ["Golang入门"]
description: Win10+WSL配置Golang开发环境
photos: http://ww1.sinaimg.cn/large/c55a7aeely1ficnszlhh9j20go098jrl.jpg
draft: false
---

Linux是比较理想的开发环境，先前电脑装的Ubuntu系统，流畅度、字体效果完爆Windows，但是开发之外其他的内容就很难在Linux上进行了，例如印象笔记、微信QQ传输文件等，最近又换回了Windows系统，借助win10的Linux子系统也能搭一个Linux下的开发环境。

# 安装WSL

关于如何安装WSL的操作就省略了，Google一下很多，也很简单，主要介绍一下其他的配置内容。

## Hyper

好看是第一生产力，Hyper是一个第三方的命令行工具，使用`Electron`框架开发的，可配置性很高，有丰富的主题可以选择，直接秒杀自带的黑框框，来两张图对比一下：
![wsl](/image/Snipaste_2019-04-20_22-22-46.png)
![wsl](/image/Snipaste_2019-04-20_22-25-30.png)

### 安装Hyper

直接上[官网](https://hyper.is/)下载对应的版本安装即可。

### 配置Hyper默认为WSL的terminal

打开Hyper窗口，在左上角菜单选择Eidt -> Perferences... 后会直接打开配置文件，配置文件是js文件很容易看懂。
![conf](/image/Snipaste_2019-04-20_22-28-35.png)

我们主要修改`shell`这个属性的值，这个配置可以修改Hyper默认使用的terminal类型，我们输入`wsl`的路径：

```json
shell: 'C:\\Windows\\System32\\wsl.exe',
...
shellArgs: ['bash'],   // 这是进入terminal时默认命令，可以酌情修改
```

修改完之后保存配置文件，Hyper会热加载配置，不过重启一下最好。

## zsh

zsh是Linux下的爆款shell，除了方便之外，我觉得还是好看，但是安装起来比较麻烦，好在有大神已经将安装过程打包，直接傻瓜式安装就好，具体安装可查[https://ohmyz.sh/](https://ohmyz.sh/)不过多赘述了。

# 安装Golang

安装好了好看的terminal，接下来就需要配置Golang的开发环境了，主要思路是在WSL中运行、编译Golang程序，在Windows中编写代码。

## 安装Golang

首先从官网上拷贝对应版本的安装文件地址，然后解压、配置环境即可：

```shell
# 下载安装文件
wget https://dl.google.com/go/go1.12.4.linux-amd64.tar.gz
# 解压到指定的文件夹
sudo tar zxvf go1.12.4.linux-amd64.tar.gz -C /usr/local
```

## 配置GOROOT和GOPATH

GOROOT配置就是常规的配置为go的bin文件夹就行，GOPATH因为我们需要配置为我们在Windows文件系统下的GOPATH地址，这样就可以直接在WSL的Linux环境下编译运行Golang程序了。

打开`.bashrc`文件（如果使用的式zsh的话需要打开`.zshrc`文件）：

```shell
vi ~/.bashrc
# vi ~/.zshrc
```

在其中增加如下内容：

```shell
export GOROOT=/usr/local/go
export GOPATH=/mnt/e/golang
export PATH=$PATH:$GOPATH:/usr/local/go/bin
```

- GOROOT的值设置的是刚才我们解压的安装文件的路径
- GOPATH的值设置的是Windows文件系统中的GOPATH地址，**在WSL中`/mnt`文件夹下就是Windows的文件系统**，因为我的Windows系统中的GOPATH是在E盘下的golang文件夹，所以这里配置成了`/mnt/e/golang`

使配置生效：

```shell
source ~/.bashrc
# source ~/.zshrc
```

# 测试

我在`E:\Golang\src\github.com\haijiandong\demo`文件夹下编写了一个简单的测试程序，接下来我们测试一下开发环境。

在WSL中进入并运行`main.go`文件：
![main.go](/image/Snipaste_2019-04-20_23-15-24.png)

这样配置后可以简单的替代Linux开发环境，并且也可以使用Windows办公两不误了。