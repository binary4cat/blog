---
title: 7、UmbracoNewsSite：新闻详情页
date: 2016-01-07T21:53:36+08:00
tags: ["Umbraco入门"]
categories: ["cSharp"]
permalink: Umbraco-Getting-Started-7
description: Umbraco入门教程
---
　　这一篇我们创建一个新闻详情页文档类型，然后尝试编辑新闻内容。

　　首先确定新闻详情页需要什么？应该有新闻标题、新闻内容、新闻简介、新闻缩略图；其中后两个是为了在新闻列表展示该新闻信息设置的。确定了这些，现在就可以开始编写了。<!--more-->
　　打开**Document Types**节点新建一个名为**News Post**的新文档类型，**Master Document Type**同样选择*none...*。
　　接着创建两个Tabs，分别是**Info**和**Content**。
![](/image/umbraco/backoffice38.png)

　　然后分别设置需要的几个属性：注意用来设置新闻内容的属性**Content**的数据类型设置为*Richtext Editor*(富文本编辑器);其次我们先不设置新闻缩略图这个属性，因为我们需要去对图片的数据类型做一些修改，控制图片的大小和名称。
![](/image/umbraco/backoffice39.png)

　　现在去修改设置图片数据类型。打开**Developer**节点，点击**Data Types**节点后面的三个小圆点按钮，新建一个名为**Featured Image**的新数据类型，并且选择属性编辑器为**Image Cropper**。
　　解释一下Umbraco中的数据类型(Data Types)：Umbraco中的数据类型都是Umbraco自己定义的，使用的时候只能选择，而不能去新创建一个新的数据类型，那么你会问，那我们刚刚创建的这个也是新的啊？这个确实是新的，但是就算是新的，也必须选择使用什么类型的数据类型作为属性编辑器，也就是**Property editor**这个选项的作用，可以理解为继承的关系吧，Umbraco定义的数据类型都是父类，我们需要一个子类去干实际完成的事，只要继承父类的一些特征就可以了，这就是我们在这里为什么编写新的数据类型的原因，为了让功能更加的细分。
![](/image/umbraco/backoffice40.png)

　　然后点击**Add new crop**按钮，编辑别名(存储到数据库中的字段名)和图片大小，最后先点击**Save crop**按钮保存编辑，然后点击**Save**按钮保存。
![](/image/umbraco/backoffice41.png)

　　现在我们可以设置新闻缩略图这个属性了，回到**News Post**文档类型，编辑**News Image**属性。
![](/image/umbraco/backoffice42.png)

　　然后将**News Post**类型设置成**News List**类型的子类型，因为内容详情是存在于新闻列表下面的。
![](/image/umbraco/backoffice44.png)

　　接下来去**Templates**节点下编写新闻详情页具体展现的代码。
　　先打开**News Post**模板，然后将母板页设置为**Master**。
![](/image/umbraco/backoffice43.png)

　　然后编写具体的代码，在**News Post**模板中添加如下代码：
　　解释一下这几个方法的使用：`CurrentPage`是获得当前页面的所有数据，也就是当前页面所在的文档类型的所有属性，这是一个动态类型，只能在内容发布后才能获取到实际的值；`GetPropertyValue`方法可以获取对应的名称的属性的值，需要一个字符串(属性名称)作为参数；`CreateData`是一个只读属性，用来获取当前页面创建的时间；`CreatorName`是一个只读属性，用来获取当前页面的创建者。

```html
@inherits Umbraco.Web.Mvc.UmbracoTemplatePage
@{
    Layout = "Master.cshtml";
}
<div class="container" style="padding-top:30px">
	<h3 style="text-align:center">@CurrentPage.GetPropertyValue("title")</h3>
	<p style="text-align:center">时间：@CurrentPage.CreateDate.ToLongDateString() 作者：@CurrentPage.CreatorName.ToString()</p>
	<hr/>
	@CurrentPage.GetPropertyValue("content")
</div>
```

　　代码编写完成后，我们回到内容编辑节点**Content**，点击**keji**后面的三个小圆点按钮，创建一个**News Post**类型的页面，编辑所需要的内容。如下：
![](/image/umbraco/backoffice45.png)
![](/image/umbraco/backoffice46.png)

　　点击**Save and publish**按钮，但是这时，我们还不能直接去浏览这条新闻，因为还没有设置任何可以指向这条新闻的链接。但是为了查看效果，我们可以点击**Properties**选项卡中的**Link to document**后面的地址，这样就可以查看效果了。
![](/image/umbraco/backoffice47.png)

　　最后的效果如下：
![](/image/umbraco/backoffice48.png)

　　下一篇我们将新闻添加到新闻列表页中，这样就可以方便的查看新闻了。
