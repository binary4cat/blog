---
title: 6、UmbracoNewsSite：添加新闻分类
date: 2016-01-06T16:21:30+08:00
tags: ["Umbraco入门"]
categories: ["cSharp"]
permalink: Umbraco-Getting-Started-6
description: Umbraco入门教程
---
　　上一篇做了一个简单的首页，在导航条上添加了几个新闻板块，接下来我们就添加这几个新闻板块的页面。

　　开始我们还是需要创建**Document Types**用来展示新闻列表。我们先做好规划，新闻列表只需要把新闻标题和图片展示出来就可以了，。<!--more-->
　　我们创建这个新闻列表的文档类型，同样是在**Setting--> Document Types**下面创建，创建一个名为**News List**的文档类型。注意这时的创建菜单中出现了一个选项 “*Master Document Type*”,这个选项的作用是如果你的这个模板的某些数据是继承自其他模板的，就选择那个模板为Master模板。之前没有出现是因为只有一个**Home**模板。我们的新闻页面是独立的，不需要继承，所以默认选择 “*none...*”。
![](/image/umbraco/backoffice22.png)

　　首先创建一个名为**Info**的Tabs，然后添加一个**Section Name**的属性，用来编辑新闻列表的种类。
![](/image/umbraco/backoffice23.png)

　　打开**Home**文档类型下，进入**Structure**选项卡中，将*Allow at root*后面的选项勾选上，作用是将**Home**类型设置成根节点；然后将*Allowed chile node types*后面*News List*勾选上，作用是允许**News List**作为**Home**的子节点。
![](/image/umbraco/backoffice24.png)

　　我们回到**Content**功能节点，点击“**首页**”后面的三个小圆点按钮，可以看到我们刚刚创建的**News List**类型已经可以使用了。如果这里没有出现**News List**类型的选项，而是一些提示，那么尝试刷新一下页面，如果还没有看看**Home**文档类型设置里里面是否将**News List**类型勾选为**Home**类型的子节点，记得保存。
![](/image/umbraco/backoffice25.png)

　　创建之后，进入设置界面，第一个文本框是设置这个节点的名称，而且也是网页在进入这个节点时的网址，所以一般使用英文比较好一点，因为网址中出现中文路径可能会出问题，也不太好看。
![](/image/umbraco/backoffice26.png)

　　按照同样的方法，设置其他几个节点。
![](/image/umbraco/backoffice27.png)

　　接下来我们将这几个页面分别链接到，上一篇中编写的首页导航条按钮中。
　　回到**Templates**中，将**Master**中的代码改造一下，绑定几个新闻链接，将bootstrap导航条的代码改成：

```html
<div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                <div style="text-align:center">
                    <a href="@home.Url">
                        <image src="@(home.siteLogo)" />
                    </a>
                </div>
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    @if (home.Children.Any())
                    {
                        foreach (var childPage in home.Children)
                        {
                            <li><a href="@childPage.Url">@childPage.sectionName</a></li>
                        }
                    }
                </ul>
                <ul class="nav navbar-nav pull-right">
                    <li><a>登陆</a></li>
                    <li><a>注册</a></li>
                </ul>
            </div>

        </div>
    </div>
```
　　解释一下上面的代码，主要是`@if(home.Children.Any(){......})`这段代码，`home`是当前网页的对象，`home.Children`是当前页面的子页面，也就是**Home**节点下的子节点，就是我们刚才设置的几个**News List**类型的节点。

　　这时我们已经可以对导航条上的按钮进行点击了，而且可以进行正确的跳转，因为没有内容，而且没有将**News List**的templates设置master模板，所以页面什么都没有。
![](/image/umbraco/backoffice28.png)
![](/image/umbraco/backoffice29.png)
![](/image/umbraco/backoffice30.png)
![](/image/umbraco/backoffice31.png)

　　进入**Templates**功能节点，打开**News List**，将Master template选项设置成**Master**。并且保存起来。
![](/image/umbraco/backoffice32.png)

　　然后在**News List**中添加一行示例代码。如下图：
![](/image/umbraco/backoffice33.png)

　　现在就可以看到我们需要的效果了：
![](/image/umbraco/backoffice34.png)
![](/image/umbraco/backoffice35.png)
![](/image/umbraco/backoffice36.png)
![](/image/umbraco/backoffice37.png)

　　下一篇我们添加新闻详细内容页面，并且在将其展示在新闻列表页面中。
　　
