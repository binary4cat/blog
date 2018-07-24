---
title: Go命令操作工具
date: 2017-08-09T21:09:03+08:00
categories: ["Golang"]
tags: ["Golang入门"]
permalink:
description:
photos: http://ww1.sinaimg.cn/large/c55a7aeely1ficnszlhh9j20go098jrl.jpg
---
## 查看Go命令
可以在命令行中执行`go`命令来查看所有的go命令：
![](http://ww1.sinaimg.cn/large/c55a7aeely1fids4wnmohj20gj0i5t9i.jpg)
<!--more-->
## Go命令
### go build
主要用来编译代码，在编译的过程中，可以同时编译代码中引用的其他包。       
- 对于普通包，执行`go build`后不会生成任何文件。(执行`gi install`可以在`$GOPATH/pkg`目录下生成对应的文件)
- 如果是main包，执行`go build`后，会在当前的目录下生成一个可执行文件。(使用`go install`或`go build -o [path]/a.exe`可以在`$GOPATH/bin`目录下生成相应的文件)
- `go build`默认会编译当前目录下的所有go文件；如果只想编译某一个文件，可以在`go build`之后加上文件名，例如：`go build name.go`。
- 使用`go build -o namexx.exe`可以指定编译输出的文件名；默认的名称有两种情况：如果当前不是main包，那么就是package名；如果是main包那么就是第一个源文件的文件名。
- `go build`会忽略目录下以“`_`”或者“`.`”开头的文件。
- 如果需要针对不同的操作系统进行不同的处理，可以根据不同系统名称后缀来命名文件，例如有一个读取数组的程序，针对不同的操作系统的执行代码有差异的，那么就有这么几个源文件：`array_linux.go`、`array_darwin.go`、`array_windows.go`、`array_freebsd.go`，在不同的操作系统下编译会选择相应系统名结尾的文件，忽略其他文件。

#### 参数介绍
- `-o` 指定输出的文件名，可以带路径输出：`go build a/b/c/name.exe`
- `-n` 把需要执行的编译命令打印出来，但是不执行，只输出。
- `-v` 打印出正在编译的包名
- `-x` 和`-n`相同，只是`-x`在打印的同时会执行编译

### go clean
这个命令是用来删除掉当前包中编译生成的文件，只剩下源文件。
#### 参数介绍
- `-i` 清除使用`go install`安装的文件(关联的安装包和可运行文件)。
- `-n` 把需要执行清除命令打印出来，但是不执行。
- `-r` 循环清除在import中引入的包。
- `-x` 和`-n`一样，只不过在打印的同时会执行清除命令。

### go fmt
这个命令可以格式化你源文件中的代码，消除源文件中的编码不规范。
#### 参数介绍
- `-l` 列出需要格式化的文件。
- `-w` 把格式化后的内容直接写入对应的源文件中。
- `-s` 简化文件中的代码。
- `-d` 打印格式化前后的内容对比，不写入文件，默认是false。
- `-e` 打印所有的语法错误。如果没有这个参数，默认只会打印前10个错误。

### go get
这个命令用来拉取远程包，并执行`go install`命令安装。
支持从BitBucket、GitHub、Google Code和Launchpad获取包文件，获取是需要安装对应的源码版本管理工具：

| 远程 | 工具 |
| :--- | :--- |
| BitBucket  | Mercurial |
| GitHub  | Git |
| Google Code Project Hosting | Git, Mercurial, Subversion |
| Launchpad  | Bazaar |
#### 参数介绍
- `-d` 只下载包，不安装(不执行`go install`)。
- `-u` 强制从网络更新包和包的依赖。
- `-f` 只在包含了`-u`参数的时候有效，不让`-u`去验证import的包是否都获取了。
- `-t` 同时获取需要为运行测试所需要的包。
- `-v` 显示执行的命令。

### go install
这个命令用来生成可执行文件或者`.a`包，并且会把编译好的文件移动到`$GOPATH/pkg`或者`$GOPATH/bin`目录下。
#### 参数介绍
支持`go build`的所有参数。

### go test
这个命令会自动读取源文件目录下面名称为`*_test.go`的文件，生成并运行测试用的可执行文件，输出测试结果。

### go tool
这个命令下面有很多参数命令。
#### 参数介绍
- `fix` 修改老版本的代码到新版本。
- `vet directory|files` 用来分析当前目录下的代码是否编写正确(参数、函数、无用代码之类)
