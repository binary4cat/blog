---
title: 6、将博客部署到coding并绑定域名
date: 2016-03-08T21:52:33+08:00
tags: ["Hexo博客搭建"]
categories: ["Blog"]
permalink: Hexo-Getting-Started-6
description: Hexo博客搭建
---
　　由于墙的原因，github访问速度很慢，而且github屏蔽了百度蜘蛛，所以将博客部署到github对国内的访客来说，并不是最好的选择，这一篇我们介绍一下，怎么将博客同时部署到github和coding上，然后绑定域名，并且分别指向国外和国内线路。

# 1. 将博客同时部署到coding上

　　之前是gitcafe+github的部署方式，奈何gitcafe被coding给收了，感叹世事变幻无常啊。还好coding也提供了pages服务，我们可以将博客部署到coding上。
　　<!--more-->
## 1.1. 注册coding账号并创建项目

　　去[www.coding.net](https://www.coding.net)注册一个账号，同时新建一个公开项目，项目名称与你的coding用户名一致。
![](http://ww1.sinaimg.cn/mw690/c55a7aeejw1f1pttpkn9ej20oz0h90th.jpg)

　　项目新建完成后，进入pages菜单，开启Pages服务：
![](http://ww2.sinaimg.cn/mw690/c55a7aeegw1f28b69cyatj20r90eit8y.jpg)

　　然后绑定我们自己的域名(如果有的话)。
![](http://ww2.sinaimg.cn/mw690/c55a7aeegw1f3u77wuci2j20jn06nq31.jpg)

　　现在需要上传我们的博客到coding.net网站，这时候，我们需要打开`_config.yml`文件填写部署到coding.net的地址。
　　1. 打开博客根目录下的`_config.yml`文件。
　　2. 找到`deploy`这个节点(就是配置部署到github地址的那个)。
　　3. 修改代码为：
　　4. 
```yaml
deploy:
  type: git
  repo:
  github: https://github.com/username/username.github.io.git,master
  gitcafe: https://git.coding.net/hexoDemo/hexoDemo.git,master
```

　　4. 同样的，`gitcafe`的值就是你的博客在coding.net上面的项目地址，后面的master表示要部署的分支。
　　5. 保存文件，执行部署命令`hexo d -g`
　　6. 会提示你输入账号密码，很麻烦不是？下一边博客写一下怎么使用SSH传输，这样就不用每次提交都输入账号密码这么麻烦了。
　　7. 不出意外，你已经上传成功了，如果没成功，请留言给我，我或许能帮到你。

　　现在我们的博客已经部署到coding上了，但是coding规定了只有`coding-pages`这个分支下的代码才能使用pages服务，所以我们需要创建这个默认分支。
　　新建一个名为`coding-pages`分支，并设置为默认分支
![](http://ww1.sinaimg.cn/mw690/c55a7aeegw1f3u8fzcaatj20yt0bsglt.jpg)![](http://ww3.sinaimg.cn/mw690/c55a7aeegw1f3u8m31zgxj20h5088dfs.jpg)

　　最后，我们需要修改`_config.yml`文件中coding.net部署的分支名：

```yaml
deploy:
  type: git
  repo:
  github: https://github.com/username/username.github.io.git,master
  gitcafe: https://git.coding.net/hexoDemo/hexoDemo.git,coding-pages
```

到目前，我们的博客已经正常部署到github、coding.net两大网站了。接下来我们需要解析域名分别引流到这两个网站，将国内的访问指向coding，国外的指向github。
由于排版的问题，解析域名在下一篇中单独写。
