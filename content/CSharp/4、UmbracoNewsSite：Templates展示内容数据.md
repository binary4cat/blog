---
title: 4、UmbracoNewsSite：Templates展示内容数据
date: 2016-01-04T16:45:02+08:00
tags: ["Umbraco入门"]
categories: ["Umbraco"]
permalink: Umbraco-Getting-Started-4
description: Umbraco入门教程
---
　　接着上一篇博客内容，这一篇我们来看看怎么把设置好的数据展示在网页上面。

　　因为Umbraco是基于MVC编写的软件，我们也最好遵循这个方式，也就是一个模板页(master)，然后再使用部分页(partial view)展示具体的内容。这里的模板需要创建在**Setting-->Templates**节点下面。我们打开**Templates**节点，可以看到我们创建的Home模板已经在这里了。<!--more-->
　　点击**Templates**后面的三个小圆点按钮，在弹出的菜单中选择**Create**创建一个新模板，作为master模板。创建之后于可以看到Umbraco已经自动为我们生成了一段代码。
![](/image/umbraco/backoffice13.png)

　　我们将这段Html代码添加到Master模板页中，
```
@inherits Umbraco.Web.Mvc.UmbracoTemplatePage
@{
    Layout = null;
    var home = @CurrentPage.Site();  //获得当前页面(当前展示的是Home，所以可以拿到Home文档类型设置的数据)
}
<!DOCTYPE html>
<html lang="zh-cn">
  <head>
    <!-- Meta tags -->
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
  </head>

  <body>
    <header>
    <a href="@home.Url">
        <image src="@(home.siteLogo)?height=65&width=205"/>
    </a>
	站点名称：<a>@home.siteTitle</a>
    </header>
	<hr/>
        @RenderBody()
	<hr/>
    <footer style="margin:0">
     	<div>&copy;  @DateTime.Now.Year-UmbracoNewsSite</div>
    </footer>
  </body>
</html>
```

　　将下面这段代码添加到**Hemo**模板中：
```
@inherits Umbraco.Web.Mvc.UmbracoTemplatePage
@{
    Layout = "Master.cshtml";
}

@CurrentPage.GetPropertyValue("content") <!--该方法获得指定名称的属性的值-->
```
　　然后选择**Home**模板，进入**Properties**选项卡，选择母版页(Master template)，将**Home**模板的母版页设置为我们刚刚设置的**Master**。
![](/image/umbraco/backoffice14.png)

　　最后我们访问一下目前为止做的网站，看看效果。
![](/image/umbraco/backoffice15.png)

　　可以看到，我们设置的属性数据已经显示在网页上面了(没有样式很难看，请不要在意这些细节😛)。其中获得当前页面数据的代码会在后面说明，到目前为止，我们已经能感受到Umbraco是如何工作的了，在下一篇，我们引入bootstrap，稍微美化一下我们的网站，然后继续添加其他内容。
