---
title: 9、UmbracoNewsSite：分页
date: 2016-01-09T13:54:45+08:00
tags: ["Umbraco入门"]
categories: ["cSharp"]
permalink: Umbraco-Getting-Started-9
description: Umbraco入门教程
---
　　接着上一篇博客的内容，这一篇我们开始给新闻列表进行分页。

　　首先说一下分页的思路，由于Umbraco使用的是模板的结构，所以我们需要将分页的页码数发送到当前的页面中，然后拿到这个页码数，对应的进行读取对应页码的新闻。
　　首先给当前的新闻分类添加了10条新闻，然后按照每页显示3条来进行分页。<!--more-->
![](/image/umbraco/backoffice54.png)

　　现在需要对**News List**模板中的代码进行编写，重新编写的代码如下：

```html
@inherits Umbraco.Web.Mvc.UmbracoTemplatePage
@{
    Layout = "Master.cshtml";
    int count = CurrentPage.Children.Count();  //拿到当前新闻分类的新闻总数
    int pageCount = 1;   //默认的页码为1
    int currentPageIndex = 1;  //默认当前为第一页
    if (count % 3 <= 0)
    {
        pageCount = count / 3;
    }
    else
    {
        pageCount = (count / 3) + 1;
    }
    if (!int.TryParse(HttpContext.Current.Request.QueryString["Page"], out currentPageIndex))
    {
        currentPageIndex = 1;//第一次进入页面的时候，还没有传递“page”，需要让currentPageIndex等于1(第一页)
    }
    var newsLists = Model.Content.Children().OrderByDescending(o => o.CreateDate).Skip((currentPageIndex - 1) * 3).Take(3); //按照新闻创建时间倒序排列
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
                        <h4 style="font-family:'Microsoft YaHei'">@post.GetPropertyValue("title")</h4><b>@post.CreateDate.ToLongDateString()</b>
                        <p>@Umbraco.Truncate((post.GetPropertyValue("introduction")).ToString(),100,true)</p>
                    </div>
                </a>
            </div>
            <hr />
        }

        <nav>
            <ul class="pagination pagination-lg">
                <li>
                    <a href="?page=@(currentPageIndex-1<1?1:currentPageIndex-1)" aria-label="上一页">
                        上一页
                    </a>
                </li>
                @for (int i = 1; i <= pageCount; i++)
                {
                    <li class="@(currentPageIndex==i?"active":"")"><a href="?page=@i">@i</a></li>
                }
                <li>
                    <a href="?page=@(currentPageIndex+1>=pageCount?pageCount:currentPageIndex+1)" aria-label="下一页">
                        下一页
                    </a>
                </li>
            </ul>
        </nav>

    </div>
</div>

```

　　分页后的效果如下：

默认进入新闻分类时，应该显示第一页的数据：
![](/image/umbraco/backoffice55.png)
点击按钮**2**时：
![](/image/umbraco/backoffice56.png)
点击按钮**1**时和刚进入页面时的地址是不同的，注意这一点：
![](/image/umbraco/backoffice57.png)

　　下一篇，我们继续创建在首页显示新闻聚合页面，将几个板块新闻中的最新的新闻显示出来。
