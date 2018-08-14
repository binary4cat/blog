---
title: 2、Umbraco后台管理功能介绍
date: 2016-01-02T11:13:41+08:00
tags: ["Umbraco入门"]
categories: ["Umbraco"]
permalink: Umbraco-Getting-Started-2
description: Umbraco入门教程
---
　　上一篇博客介绍了Umbraco的安装，这一篇里面介绍一下umbraco的后台管理的一些主要功能的使用方式。
　　首先介绍一下侧边栏的几个主要功能：
<!--more-->
　　　　1、**Content**：从字面意思就可以看出来，这个功能是用来编辑网站内容的。
　　　　2、**Media**：这是用来管理多媒体的功能，Umbraco支持图片、视频、音频，可以在这里进行统一的管理。
　　　　3、**Settings**：这个功能是设置网站的一些代码、样式、还有对模板的一些操作；我们之后的编写操作会主要在这里进行。
　　　　4、**Developer**：这是Umbraco的一些操作，设置数据库字段、安装功能和安装包、编辑Macros。
　　　　5、**User**：这是对Umbraco网站后台的管理账号进行管理的，也就是管理员账号。
　　　　6、**Members**：这个是针对网站会员的管理，如果是能够登陆的网站，可以在这里设置会员。
　　　　7、**Forms**:这个是对编写网站表单的模块。
![](/image/umbraco/backoffice1.png)

　　由于其他几个功能需要在网站模板设置之后才能进行编辑，这里就先主要介绍一下比较重要的**Setting**这个功能选项。
　　点开**Setting**选项，我们可以看到几个文件夹图标的功能，这些功能都是用来编辑网站样式和功能代码的，我们一一介绍一下：
　　**Stylesheets**：这里是编辑网站的style样式的，css代码可以写在这里。使用的时候只需要创建新文件，并且直接将css代码写在文件中就可以了。
　　**Templates**：这里是编辑模板代码的选项，相当于MVC中的view模块，而且代码也是使用MVC中view的写法，也就是*razor*语法。
　　**Partial Views**：部分视图功能，相当于MVC中的`@Html.Partial()`语法使用的视图代码。
　　**Scripts**：编辑创建javascript脚本的功能。
　　**Dictionary**：字典，如果你的网站是面向国际用户的，在这里可以设置多语言功能。
　　**Languages**：语言选项，设置网站语言。
　　**Media Types**：媒体类型，设置网站用到的媒体类型，比如设置上传图片的大小之类的功能。
　　**Document Types**：文档类型，这可以说是umbraco中最重要的部分，文档类型对应着网站应该显示什么内容，延伸到数据库中就是设置网站中要显示的数据字段。
    ![](/image/umbraco/backoffice3.png)
　　
　　这一篇简单的介绍了一下umbraco中的一些设置，其他的在之后的篇幅中，将会继续介绍。从下一篇开始，我们从一个UmbracoNewsSite网站开始，逐步的实际体验学习Umbraco的使用。
　　
