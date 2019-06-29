---
title: 'Win10+WSL配置Golang开发环境'
date: 2019-04-20T22:08:09+08:00
categories: ['Golang']
tags: ['Golang入门']
description: Win10+WSL配置Golang开发环境
photos: http://ww1.sinaimg.cn/large/c55a7aeely1ficnszlhh9j20go098jrl.jpg
draft: false
---

Linux 是比较理想的开发环境，先前电脑装的 Ubuntu 系统，流畅度、字体效果感觉都比 Windows 好，但是开发之外其他的内容就很难在 Linux 上进行了，例如印象笔记、微信 QQ 传输文件等，最近又换回了 Windows 系统，花时间借助 win10 的 Linux 子系统也能搭一个 Linux 下的开发环境。

# 安装 WSL

关于如何安装 WSL 的操作就省略了，Google 一下很多，也很简单，主要介绍一下其他的配置内容。

## 配置清华大学软件源

Ubuntu 官方阿软件源服务器在国外，执行`apt-get`比较慢，所以切换为国内的软件源进行加速，这里使用[清华大学开源软件镜像站提供的 Ubuntu 镜像](https://mirror.tuna.tsinghua.edu.cn/help/ubuntu/)

## Hyper

好看是第一生产力，Hyper 是一个第三方的命令行工具，使用`Electron`框架开发的，可配置性很高，有丰富的主题可以选择，直接秒杀自带的黑框框，来两张图对比一下：
![wsl](/image/Snipaste_2019-04-20_22-22-46.png)
![wsl](/image/Snipaste_2019-04-20_22-25-30.png)

### 安装 Hyper

直接上[官网](https://hyper.is/)下载对应的版本安装即可。

### 配置 Hyper 默认为 WSL 的 terminal

打开 Hyper 窗口，在左上角菜单选择 Eidt -> Perferences... 后会直接打开配置文件，配置文件是 js 文件很容易看懂。
![conf](/image/Snipaste_2019-04-20_22-28-35.png)

我们主要修改`shell`这个属性的值，这个配置可以修改 Hyper 默认使用的 terminal 类型，我们输入`wsl`的路径：

```json
shell: 'C:\\Windows\\System32\\wsl.exe',
...
shellArgs: ['bash'],   // 这是进入terminal时默认命令，可以酌情修改
```

修改完之后保存配置文件，Hyper 会热加载配置，不过重启一下最好。

## zsh

zsh 是 Linux 下的爆款 shell，除了方便之外，我觉得还是好看，但是安装起来比较麻烦，好在有大神已经将安装过程打包，直接傻瓜式安装就好，具体安装可查[https://ohmyz.sh/](https://ohmyz.sh/)不过多赘述了。

# 安装 Golang

安装好了好看的 terminal，接下来就需要配置 Golang 的开发环境了，主要思路是在 WSL 中运行、编译 Golang 程序，在 Windows 中编写代码。

## 安装 Golang

首先从官网上拷贝对应版本的安装文件地址，然后解压、配置环境即可：

```shell
# 下载安装文件
wget https://dl.google.com/go/go1.12.4.linux-amd64.tar.gz
# 解压到指定的文件夹
sudo tar zxvf go1.12.4.linux-amd64.tar.gz -C /usr/local
```

## 配置 GOROOT 和 GOPATH

GOROOT 配置就是常规的配置为 go 的 bin 文件夹就行，GOPATH 因为我们需要配置为我们在 Windows 文件系统下的 GOPATH 地址，这样就可以直接在 WSL 的 Linux 环境下编译运行 Golang 程序了。

打开`.bashrc`文件（如果使用的式 zsh 的话需要打开`.zshrc`文件）：

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

- GOROOT 的值设置的是刚才我们解压的安装文件的路径
- GOPATH 的值设置的是 Windows 文件系统中的 GOPATH 地址，**在 WSL 中`/mnt`文件夹下就是 Windows 的文件系统**，因为我的 Windows 系统中的 GOPATH 是在 E 盘下的 golang 文件夹，所以这里配置成了`/mnt/e/golang`

使配置生效：

```shell
source ~/.bashrc
# source ~/.zshrc
```

## 测试

我在`E:\Golang\src\github.com\haijiandong\demo`文件夹下编写了一个简单的测试程序，接下来我们测试一下开发环境。

在 WSL 中进入并运行`main.go`文件：
![main.go](/image/Snipaste_2019-04-20_23-15-24.png)

这样配置后可以简单的替代 Linux 开发环境，并且也可以使用 Windows 办公两不误了。

# 安装 Docker

如果直接用 apt 来安装 docker，不会是最新版的，所以参考[官方文档](https://docs.docker.com/install/linux/docker-ce/ubuntu/)来安装最新版的

```shell
sudo apt-get remove docker docker-engine docker.io
sudo apt-get update
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
sudo apt-get update
sudo apt-get install docker-ce
```

执行一下`docker version`:

![docker](/image/wx_2019164102.png)

启动 Docker：

```shell
sudo service docker start
```

此时直接运行镜像会报错：

![docker](/image/wx_20190420165031.png)

因为 WSL 不是一个完整阿 Linux 系统，所以没有办法运行 Docker 的守护进程，如果在 Windows 中安装了 Docker，那么在 WSL 中可以操作 Windows 安装的 Docker。

## 在 WSL 中操作 Docker

首先启用 Windows10 系统的**Hyper-V**，然后下载安装[Docker for Windows](https://hub.docker.com/editions/community/docker-ce-desktop-windows)并运行（Hyper-V 只支持 win10 Pro 以上系统，其他系统需要使用[docker toolbox](https://docs.docker.com/toolbox/toolbox_install_windows/)安装）。

然后打开 Docker for Windows 的配置，勾选`Expose daemon on tcp:localhost:2375 without TLS`

![docker](/image/wx_20190420165951.png)

配置 WSL 中的 Docker 链接到 Docker for Windows：

使用命令`vi ~/.bashrc`（`vi ~/.zshrc`），增加下面的配置：

```shell
export DOCKER_HOST=tcp://127.0.0.1:2375
```

保存后执行`source ~/.bashrc`（`vi ~/.zshrc`）使配置生效。

## 执行 Docker 操作

![docker](/image/wx_20190420173017.png)
