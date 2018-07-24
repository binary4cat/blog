---
title: 3、UmbracoNewsSite：文档类型设置
date: 2016-01-03T13:37:04+08:00
tags: ["Umbraco入门"]
categories: ["Umbraco"]
permalink: Umbraco-Getting-Started-3
description: Umbraco入门教程
---
　　从这一篇开始，我们制作一个简单的新闻网站UmbracoNewsStie，用来演示Umbraco的实际使用技巧。

　　在开始，我们需要先创建文档类型才能进行其他的操作，文档类型就是网站要展示什么。<!--more-->
　　首先我们先做一个网站的首页，进入Umbraco的后台界面，选择**Setting**，点击**Document Types**选项右边的三个小圆点按钮，在弹出的菜单中点击**Create**。
![](/image/umbraco/backoffice4.png)

　　在创建表单中输入这个文档类型的名称**Home**，然后勾选创建对应的模板(Create Matching template),最后点击**Create**按钮。
![](/image/umbraco/backoffice5.png)

　　然后我们就进入了设置界面。可以在上面看到四个选项卡：**Info、Structure、Generic properties、Tabs**，Info设置当前文档类型的名称、别名(也就是数据库中存储的字段名)、图标等信息；Structure设置当前文档类型所处的结构，比如是否是根节点、其下是否需要其他节点等；Generic properties设置当前文档类型的一些属性(需要显示在网页上的字段)，Tabs设置当前文档类型在使用时的分类，结合Generic proerties使用，比如我们创建一个名为"Info"的Tabs，那么这个Info就是用来设置首页的信息字段的，还可以设置content等等Tabs。
![](/image/umbraco/backoffice6.png)

　　我们首先创建两个Tabs，分别是首页的信息Tabs和内容Tabs。 ps:选项后面的0和1的数字是表示的Tabs显示的顺序序号，默认就可以了。
![](/image/umbraco/backoffice7.png)

　　进入Generic properties选项卡，分别创建Info和Content需要用到的字段：站点名称、站点logo，首页内容。
　　点击**Click here to add a new property**，创建站点名称，如下图：
　　Name表示该属性的名称，可以设置为中文；Alias是要设置的别名，是要设置到数据库中的字段；Type表示数据类型，这里的数据类型是Umbraco自己设置的数据类型；Mandatory表示用到这个属性时，是不是需要强制设置这个属性的值；Validation用来设置一个正则表达式，用来验证该属性的值；Description设置该属性的解释说明。
　　填写完毕后点击右上角的**Save**保存按钮。
![](/image/umbraco/backoffice8.png)

　　按照同样的方式设置其余的两个属性，注意Site Logo的类型是Upload类型。
![](/image/umbraco/backoffice9.png)

　　设置完了文档类型的字段，我们试试创建一个网页，检验一下我们设置的这些字段，也感受一下umbraco是怎么工作的。
　　接下来选择**Content**选项，点击后面的三个小圆点按钮，在弹出的菜单中选择要创建的文档类型，这里只创建了一个Home，我们就选择这个。
![](/image/umbraco/backoffice10.png)

　　点击**Create**按钮之后，会弹出设置界面，这是我们会发现，我们在**Home**文档类型中设置的**Info**和**Content**这两个Tabs都在这里出现了，而且在设置在其下的属性也都在这里。
　　我们分别填写这几个内容和上传一张图片作为logo。
![](/image/umbraco/backoffice11.png)![](/image/umbraco/backoffice16.png)

　　最后点击**Save and publish**按钮进行保存和提交发布。发布之后，我们的设置的属性字段，就存储到了Umbraco的数据库中，接下来，我们就要设置如何展示这些属性的数据。
![](/image/umbraco/backoffice12.png)

　　
