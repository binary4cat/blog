---
title: 3、Hexo博客配置和主题
date: 2016-03-05T23:16:49+08:00
tags: ["Hexo博客搭建"]
categories: ["Blog"]
permalink: Hexo-Getting-Started-3
description: Hexo博客搭建
---
　　这一篇中，我们介绍一下对Hexo博客的配置，和主题的一些设置。

# 1. 博客网站信息配置

　　在创建的博客文件夹根目录(比如我的是E:\Is2Blog),找到名为`_config.yml`的配置文件，使用编辑器打开，具体的配置信息说明在下面：<!--more-->

```yaml
# Hexo Configuration
## Docs: https://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site
title: Hexo博客搭建                #主标题
subtitle: Hexo博客很酷很炫很有逼格   #副标题
description: 这是给搜索引擎看的，百度蜘蛛你喜欢我这句话吗？  
author: 人民群众        #网站作者名称
language: zh-CN   #语言
timezone: #不用填写

# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'

url: http://yoursite.com  如果有域名的话，可以在这里填写你指向的域名
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:

# Directory

source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:

# Writing   文章编写有关的设置，默认即可

new_post_name: :title.md # File name of new posts
default_layout: post
titlecase: false # Transform title into titlecase
external_link: true # Open external links in new tab
filename_case: 0
render_drafts: false
post_asset_folder: false
relative_link: false
future: true
highlight:
  enable: true
  line_number: true
  auto_detect: false
  tab_replace:

# Category & Tag  标签和分类，这里不用修改

default_category: uncategorized
category_map:
tag_map:

# Date / Time format   时间格式，默认即可
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/

date_format: YYYY-MM-DD
time_format: HH:mm:ss

# Pagination
## Set per_page to 0 to disable pagination

per_page: 10        #表示每页显示多少条博客，可以修改
pagination_dir: page

# Extensions
## Plugins: https://hexo.io/plugins/   
## Themes: https://hexo.io/themes/

theme: landscape     #这里是主题名称，使用哪个主题，这里就填写相应的主题名

# Deployment
## Docs: https://hexo.io/docs/deployment.html

deploy:
  type: git
  repository: https://github.com/username/username.github.io.git
  branch: master
```
　　配置完成后，我们刷新一下网站(本地预览可以直接刷新，如果网络访问需要生成部署才能看到改变，建议本地预览，满意后再部署)：
![](http://ww3.sinaimg.cn/mw690/c55a7aeejw1f1mf831ho1j212n0m1dos.jpg)
![](http://ww2.sinaimg.cn/mw690/c55a7aeejw1f1mfao9xf8j212n0m1416.jpg)


# 2. 主题的配置

　　Hexo有很多好看有特色的主题，安装使用也非常方便，基本上都在github上开源了， 而且都有使用文档，很容易配置。
　　这里就使用默认的主题，介绍一下Hexo主题的一些设置修改的方法。更多主题请访问：https://hexo.io/themes/  或者 

　　打开博客根目录下的`themes`文件夹，可以看到这里面有主题文件夹，这里我们打开`landscape`文件夹，里面有文件夹和文件，主题的配置文件就是`_config.yml`这个文件。
　　使用编辑器打开：

```yaml
# Header
menu:  #这是标题栏的配置
  #Home: /
  #Archives: /archives
  首页: /   #表示根目录，也就是首页
  存档: /archives
  关于: /about    #关于页面需要自己创建，文章后面有创建方法
rss: /atom.xml   #RSS订阅

# Content
#excerpt_link: Read More
excerpt_link: 阅读更多    
fancybox: true   #一个JavaScript的图片浏览组件，参照我的博客中，点击图片后弹出图层，可以左右浏览的效果，true为打开，false为关闭

# Sidebar
sidebar: right   #侧边栏的位置
widgets:        #侧边栏小工具组件
- category      #分类
- tag           #标签
- tagcloud      #标签云
- archive       #存档
- recent_posts  #最新发布

# display widgets at the bottom of index pages (pagination == 2)
index_widgets:
# - category
# - tagcloud
# - archive

# widget behavior
archive_type: 'monthly'   #归档时以月为单位记录，参考我的侧边栏
show_count: false         #是否显示数量

# Miscellaneous   #社交信息，都是国内被墙掉的，就不设置了。
google_analytics:
favicon: /favicon.png
twitter:
google_plus:
fb_admins:
fb_app_id:
```

**添加关于页面**
　　在“Git Bash Here”命令行窗口输入：`hexo new page "about"` 该命令创建一个新的页面，在博客根目录下依次打开`source/about/index.md`，然后在里面编辑内容即可。
![](http://ww3.sinaimg.cn/mw690/c55a7aeejw1f1nj1krgugj212n0c2jzb.jpg)
