---
title: 8、UmbracoNewsSite：添加新闻列表
date: 2016-01-08T12:20:45+08:00
tags: ["Umbraco入门"]
categories: ["cSharp"]
permalink: Umbraco-Getting-Started-8
description: Umbraco入门教程
---
　　在上一篇中添加了新闻详情页面，但是没有任何可以指向这条新闻内容的跳转，所以需要将新闻添加到新闻列表中展示。

　　现在我们找到**News List**模板，对其中的代码进行编辑，以展示新闻列表。<!--more-->
![](/image/umbraco/backoffice49.png)

　　**News List**模板的代码如下：
　　解释一下这段代码：`Model`在Umbraco是也是起到和MVC中的Model一样的作用，和`CurrentPage`的作用都是获得数据，但是`CurrentPage`是动态类型，只能在内容发布后才能拿到数据，相对`Model`来说可能书用起来比较麻烦，不如`Model`更加灵活一些。
　　`OrderByDescending`是Umbraco自己重写的排序方法，使用和原生的Linq一样，都是lambda表达式的方式。
　　`GetCropUrl`方法是用来获得*crop*图片的路径的方法，需要两个参数，第一个是设置图片的属性的名字，第二个是数据类型中设置的**Crop name**。
　　`Umbraco.Truncate`是Umbraco实现的一个方法，用来截取指定长度的字符，有8个重载，在这里用到了三个参数的重载，这三个参数分别是：要截取的字符串；截取的长度；是否显示省略号。

```html
@inherits Umbraco.Web.Mvc.UmbracoTemplatePage
@{
    Layout = "Master.cshtml";
    var newsLists = Model.Content.Children().OrderByDescending(o => o.CreateDate); //按照新闻创建时间倒序排列
}
<div class="container">
    <div class="row">
        @foreach (var post in newsLists)
        {
            <div class="row" style="margin-top:10px">
                <a href="@post.Url">
                    <div class="col-lg-4 col-sm-6">
                        <img src="@post.GetCropUrl("newsImage", "newsImage")" class="img-responsive img-thumbnail" />
                    </div>
                    <div class="col-lg-8 col-sm-6" style="color:#000000">
                        <h3>@post.GetPropertyValue("title")</h3><b>@post.CreateDate.ToLongDateString()</b>
                        <p>@Umbraco.Truncate((post.GetPropertyValue("introduction")).ToString(),150,true)</p>
                    </div>
                </a>
            </div>
            <hr />
        }
    </div>
</div>
```

　　现在看一下初步的效果，可以看到已经能在新闻列表页看到新闻的缩略图、标题和简介了。
![](/image/umbraco/backoffice50.png)

　　我们多添加些新闻，在下一篇博客将对新闻列表进行分页处理，并且在首页对几个新闻板块的新闻做一个概览，也就是把最新的新闻展示在首页。
　　再此之前，我们对**Content**中的编辑器做一些调整，我们现在编辑的每条新闻都是在**keji**这个节点下面的，如果添加新闻很多，管理起来也不方便，所以我们需要设置一下，
![](/image/umbraco/backoffice51.png)

　　进入**Document Types**功能下，打开**News List**类型的*Structure*选项卡，勾选**Enable list view**选项，然后保存。因为我们要对**News List**这个节点下的内容进行管理，所以要对**News List**文档类型进行设置。
![](/image/umbraco/backoffice52.png)

　　现在回到**Content**节点下，可以看到原本存在于**keji**节点下的新闻详细内容页，可以使用表格的形式进行管理了。新闻内容再多也可以很容易的进行管理啦😄
![](/image/umbraco/backoffice53.png)
