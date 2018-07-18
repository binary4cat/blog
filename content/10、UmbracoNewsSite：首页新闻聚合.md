title: 10、UmbracoNewsSite：首页新闻聚合
date: 2016-01-10T12:56:07+08:00
tags: Umbraco入门
categories: Umbraco
permalink: Umbraco-Getting-Started-10
description: Umbraco入门教程
---
　　目前为止已经实现了基本的新闻展示和新闻内容添加管理的功能演示，这一篇是Umbraco入门的最后一篇教程，我们看看怎么在首页拿到其他页面的数据的。

　　要在首页拿到几个新闻板块的节点，然后分别读取最新的新闻数据显示在首页中。
　　我们需要在**Home**模板中进行编写代码，具体的代码如下：<!--more-->
　　其中比较关键的一个方法就是`AncestorOrSelf()`，这个方法能够拿到指定层级的节点数据。
```
@inherits Umbraco.Web.Mvc.UmbracoTemplatePage
@{
    Layout = "Master.cshtml";
    var selectClass = Model.Content.AncestorOrSelf(1).Children().ToList();  //拿到第一级节点(根节点)下的几个几点，也就是几个新闻板块。
    List<IPublishedContent> list = new List<IPublishedContent>();  //存储每个板块拿出来的最新的新闻。
    foreach (var item in selectClass)
    {
        //因为要展示最新的10条数据，所以每次个节点(新闻页总共有三个)取出最新的10个，存储起来(30条分别是三个板块最新的新闻，每个取10条为了避免只有一个板块更新新闻时，也显示其他板块新闻的问题)
        list.AddRange(item.Children().OrderByDescending(o => o.CreateDate).Take(10).ToList());
    }
}
<div class="jumbotron">
    <h1>@CurrentPage.GetPropertyValue("content")</h1>
</div>
<div class="container">
    <div class="row">
        <div class="col-lg-12">
            @foreach (var post in list.OrderByDescending(o => o.CreateDate).Take(10))
            {
                <div class="row" style="margin-top:10px">
                    <a href="@post.Url">
                        <div class="col-lg-4 col-sm-6">
                            <img src="@post.GetCropUrl("newsImage", "newsImage")" class="img-responsive img-thumbnail" />
                        </div>
                        <div class="col-lg-8 col-sm-6" style="color:#000000">
                            <h4 style="font-family:'Microsoft YaHei'">@post.GetPropertyValue("title")</h4><b>@post.CreateDate.ToLongDateString()</b>
                            <p>@Umbraco.Truncate((post.GetPropertyValue("introduction")).ToString(), 100, true)</p>
                        </div>
                    </a>
                </div>
                <hr />
            }
        </div>
    </div>
</div>
```

　　我已经给其他几个新闻板块添加了新闻，现在可以看看效果了：
![](/image/umbraco/backoffice58.png)

![](/image/umbraco/backoffice59.png)