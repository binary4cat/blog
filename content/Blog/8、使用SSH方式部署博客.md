---
title: 8、使用SSH方式部署博客
date: 2016-05-14T01:35:24+08:00
tags: ["Hexo博客搭建"]
categories: ["Blog"]
permalink: Hexo-Getting-Started-1
description: Hexo博客搭建
---
　　每次写完博客提交都要输入账号密码，尤其是同时部署到了俩网站，输两个网站的账号密码，简直噩梦一般，这篇就是为了解除这个噩梦。
## 什么是SSH
　　简单说就是一种更加安全的网络传输协议。摘录一段维基百科的解释：
> Secure Shell（缩写为SSH），由IETF的网络工作小组（Network Working Group）所制定；SSH为一项创建在应用层和传输层基础上的安全协议，为计算机上的Shell（壳层）提供安全的传输和使用环境。
传统的网络服务程序，如rsh、FTP、POP和Telnet其本质上都是不安全的；因为它们在网络上用明文传送数据、用户帐号和用户口令，很容易受到中间人（man-in-the-middle）攻击方式的攻击。就是存在另一个人或者一台机器冒充真正的服务器接收用户传给服务器的数据，然后再冒充用户把数据传给真正的服务器。
而SSH是目前较可靠，专为远程登录会话和其他网络服务提供安全性的协议。利用SSH协议可以有效防止远程管理过程中的信息泄露问题。通过SSH可以对所有传输的数据进行加密，也能够防止DNS欺骗和IP欺骗。
SSH之另一项优点为其传输的数据可以是经过压缩的，所以可以加快传输的速度。SSH有很多功能，它既可以代替Telnet，又可以为FTP、POP、甚至为PPP提供一个安全的“通道”。

## 创建SSH
1. 检查电脑上是否创建了SSH key
　　右键打开Git Bash，输入下面的命令，检查“.ssh”文件夹中是否已经创建了id_rsa id_rsa.pub known_hosts文件。<!--more-->
```
cd ~/.ssh
ls
```
2. 创建SSH key
　　执行下面命令
```
ssh-keygen -t rsa -C "@你的邮箱.com"
```
3. 一路回车，直到最后。
4. 进入个人文件夹，找到进入“.ssh”文件夹，使用编辑器打开`id_rsa.pub`文件，全选复制里面的内容。

## 将SSH添加到github和coding
**github**
1. 登陆你的github账户，进入个人账户设置界面。![](http://ww3.sinaimg.cn/mw690/c55a7aeegw1f3uan23rrbj20ys0akjse.jpg)
2. 将复制的内容，粘贴到“Key”框里面，Title随意填写。确认保存。![](http://ww4.sinaimg.cn/mw690/c55a7aeegw1f3uapehmq5j20u70bgmxr.jpg)
**coding**
1. 登陆你的coding账户，进入账户设置=>SHH公钥，填写赋值的内容保存。![](http://ww3.sinaimg.cn/mw690/c55a7aeegw1f3uatkjjpgj20x00d80tx.jpg)

## 将博客的部署方式改为SSH方式
1. 进入github和coding里你的博客项目，复制SSH地址。![](http://ww2.sinaimg.cn/mw690/c55a7aeegw1f3uaxsq545j20uc074wf7.jpg)
![](http://ww3.sinaimg.cn/mw690/c55a7aeegw1f3uaym7h35j20v002t74d.jpg)
2. 打开博客文件夹根目录下的`_config.yml`文件，修改`deploy`节点，将两个地址分别改为你的博客项目的SSH地址,例如：
```
deploy:
  type: git
  message: ""
  repo: 
    github: git@github.com:haijiandong/haijiandong.github.io.git,master
    gitcafe: git@git.coding.net:Is2/Is2.git,coding-pages
```

现在可以试一下了，改完第一次部署会让输入yes/no，输完yes之后，你会发现瞬间麻烦就没有了，可以安心写博客了。
