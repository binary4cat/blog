---
title: 'WSL+VSCODE+UBUNTU开发'
date: 2019-06-29T14:46:11+08:00
tags: ["WSL","VS code"]
categories: ["Tool"]
description: 'WSL+VSCODE+UBUNTU开发'
draft: false
photos:
---

# 配置 WSL 环境

首先安装 WSL，我这里选择的是 ubuntu18.04 这个应用。

## 切换 WSL 的默认用户为 root 用户

切换成 root 用户主要是避免后续开发中遇到权限问题比较麻烦，直接默认 root 解决问题。

找到`ubuntu`安装目录，一般在`C:\Program Files\WindowsApps\CanonicalGroupLimited.Ubuntu18.04onWindows_1804.2019.522.0_x64__79rhkp1fndgsc`文件夹下面。

在文件夹中打开 powershell 运行下面的命令：

```shell
./ubuntu1804.exe config --default-user root
# 重启WSL服务
net stop LxssManager
net start LxssManager
```

如果显示拒绝访问，就用管理员权限打开 powershell，或者直接在资源管理器中找到`LxssManager`这个服务重新启动。

## 切换 apt 源为清华源

默认的软件源下载太慢，替换为国内的清华大学软件源。

Ubuntu 清华源的地址：[https://mirror.tuna.tsinghua.edu.cn/help/ubuntu/](https://mirror.tuna.tsinghua.edu.cn/help/ubuntu/)

## 切换 WSL 默认的 shell 为 zsh

zsh 的优点在我看来就是配置丰富，最重要的就是好看。

执行下面的命令安装`zsh`和`oh-my-zsh`

```shell
sudo apt-get update
sudo apt-get install zsh
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

查看当前机器上的 shell 类型：

```shell
cat /etc/shells

# 输出下面类似内容
/bin/sh
/bin/bash
/bin/rbash
/bin/dash
/usr/bin/tmux
/usr/bin/screen
/bin/zsh
/usr/bin/zsh
```

切换登陆时默认的 shell 为 zsh

```shell
chsh -s /bin/zsh
```

重新打开 ubuntu 窗口，或者重新在 VS code 中连接 WSL，就可以使用新设置的 shell 了。

### 配置 zsh 的主题为 agnoster

```shell
vi ~/.zshrc

# 修改下面的属性：
ZSH_THEME="agnoster"
```

配置完成之后执行`source ~/.zshrc`命令生效，之后发现展示的内容有乱码，这是因为字体的问题，由于主要在 VS code 中使用，这里就不处理默认窗口的字体问题了。

# 安装 Remote-WSL 插件

在 VS code 中搜索`Remote-WSL`并安装，完成之后在左下角会出现这个图标：

![](/image/Snipaste_2019-06-28_22-17-35.png)

点击该图标，选择 New Window

![](/image/Snipaste_2019-06-28_22-19-26.png)

第一次有些慢，需要安装组件：

![](/image/Snipaste_2019-06-28_22-20-17.png)

## 切换默认终端为 zsh

进入后打开 terminal 发现默认还是 bash，需要配置一下切换为 zsh 终端：

```shell
vi ~/.bashrc

# 在打开的文件中（末尾即可）增加下面内容
if test -t 1; then
    exec zsh
fi
```

关闭掉终端再打开，就可以发现默认 shell 切换成 zsh 了。

解决乱码问题，修改 VS code 的字体即可，这里推荐[Fira Code](https://github.com/tonsky/FiraCode)字体，下载安装该字体，并修改为 VS code 的默认字体，再打开终端就是下面的效果了：

![](/image/Snipaste_2019-06-29_12-45-47.png)

## 安装 WSL 下的 VS code 扩展

进入 WSL 中时 VS code 的本地扩展都是没有的，需要在 WSL 下在安装一边，也就是点点点就行：

![](/image/Snipaste_2019-06-29_12-49-44.png)

把已安装过的插件上显示"Install on WSL"的都点击安装上就行啦。

# 测试 WSL 下的.net core 开发

来个[hello world](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install)

按照上面的官方文档在 terminal 中安装.net core：

![](/image/Snipaste_2019-06-29_13-26-52.png)

## 创建并运行

进入我们本机的指定磁盘下新建项目（本机的文件系统挂载在 WSL 中的`/mnt`文件目录下）：

```shell
# 进入我的本机e盘下面
cd /mnt/e/dotnet
dotnet new console -o myApp
cd myApp
dotnet run
```

![](/image/Snipaste_2019-06-29_14-11-35.png)

## 在 VS code 中修改编译运行

在 VS code 中编辑 myApp 的内容，然后运行：

![](/image/Snipaste_2019-06-29_14-14-04.png)

将`Hello World!`修改为`Hello WSL!`

![](/image/Snipaste_2019-06-29_14-15-07.png)

### 在 terminal 中编译为 Linux 版本运行

![](/image/Snipaste_2019-06-29_14-20-26.png)

# 结尾

至此就可以在 VS code 中体验 Linux 环境下做开发了，只要在 VS code 中连上 WSL 就可以在 Linux 下开发、编译、测试 Linux 下的应用，而不用再准备一台 Linux 机器写代码、编译测试，感觉很爽。
