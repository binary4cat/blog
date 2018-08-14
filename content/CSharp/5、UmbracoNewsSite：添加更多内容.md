---
title: 5、UmbracoNewsSite：添加css和js文件
date: 2016-01-05T10:41:40+08:00
tags: ["Umbraco入门"]
categories: ["Umbraco"]
permalink: Umbraco-Getting-Started-5
description: Umbraco入门教程
---
　　我们接着上一篇的内容，这一篇我们引入stylesheets样式，并且尝试添加其他页面的内容。

　　打开visual studio，在项目根目录下新建**js**和**css**文件夹，分别把bootstrap的文件复制进去。(如果已经有就不用新建了)
　　然后打开Umbraco后台管理页面，刷新之后就可以看到**Stylesheets**节点下面出现了刚刚复制的文件。这是因为Umbraco规定了**Stylesheet**节点默认去读取根目录下名为*css*的文件夹中的文件；看到这你可能会下意识的认为那**Scripts**节点下肯定会出现刚才复制的js脚本文件啦！事实是——没有！**Scripts**节点默认读取的文件夹名是*scripts*，所以在引用这些文件的时候要注意路径。<!--more-->
![](/image/umbraco/backoffice19.png)

　　文件添加完毕了，我们在程序中引用文件，在**Templates**节点下打开Master文件，然后将bootstrap文件分别添加进去(这里需要注意路径)。
```
<!DOCTYPE html>
<html lang="zh-cn">
  <head>
    ......
    
    <!-- CSS -->
    <link rel="stylesheet" type="text/css" href="/css/bootstrap.min.css">
  </head>

  <body>
    .......
    
    <!-- Javascripts -->
    <script src="/js/jquery.min.js"></script>
    <script src="/js/bootstrap.min.js"></script>
  </body>
</html>
```

　　接下来我们进入编码工作，为了方便编写代码，可以进入visual studio进行编写，在Umbraco后台创建的文件内容在源代码中都可以找到，其中**Templates**节点下的文件在VS项目中的Views文件夹下，这下MVC模式就可以体现出来了。为了演示，我会继续在网页后台写代码，这个没有特别的限制。
![](/image/umbraco/backoffice20.png)

　　我们把*Master*模板代码改动一下，让页面稍微好看一点。代码如下：
```
@inherits Umbraco.Web.Mvc.UmbracoTemplatePage
@{
    Layout = null;
    var home = @CurrentPage.Site();
}
<!DOCTYPE html>
<html lang="zh-cn">
<head>
    <!-- Meta tags -->
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <!-- CSS -->
    <link rel="stylesheet" type="text/css" href="/css/bootstrap.min.css">
</head>
<body>
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
                    <li><a>科技新闻</a></li>
                    <li><a>体育新闻</a></li>
                    <li><a>编程新闻</a></li>
                    <li><a>程序员新闻</a></li>
                </ul>
                <ul class="nav navbar-nav pull-right">
                    <li><a>登陆</a></li>
                    <li><a>注册</a></li>
                </ul>
            </div>

        </div>
    </div>

    <hr />
    <div class="container" style="margin-top:75px">
        @RenderBody()
    </div>
    <hr />
    <footer class="container-narrow" style="text-align:center">
        <div>&copy;  @DateTime.Now.Year-UmbracoNewsSite</div>
    </footer>
    <!-- Javascripts -->
    <script src="/js/jquery.min.js"></script>
    <script src="/js/bootstrap.min.js"></script>
</body>
</html>
```

　　将*Home*模板的代码也稍微改动一下：
```
@inherits Umbraco.Web.Mvc.UmbracoTemplatePage
@{
    Layout = "Master.cshtml";
}
<div class="jumbotron">
    <h1>@CurrentPage.GetPropertyValue("content")</h1>
</div>

```
　　改动后的页面效果如下：
![](/image/umbraco/backoffice21.png)

　　下一篇我们添加导航栏的几个页面，看看在Umbrao中怎么添加页面。
