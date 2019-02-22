---
title: HTML辅助方法
date: 2015-08-02T23:15:02+08:00
tags: ["cSharp"]
categories: ["cSharp"]
permalink: HTML-helper-methods
description: HTML辅助方法
---
　　HTML辅助方法，也就是`HtmlHelper`，就是帮助快捷的产生HTML标签的作用。
　　HTML辅助方法可以编写超链接、表单(<form>)、输入框、选项、加载其他的分布视图(Partial View)等。

# 1. 超链接：Html.ActionLink
　　`@Html.ActionLink()`用于产生超链接。并且链接的文字部分会自动的进行HTML编码(HtmlEncode)。
<!--more-->
**Html.ActionLink()方法重载**:
　　`@Html.ActionLink("链接文字","Action_Name")`：该语法生成的超链接，点击后会跳转到“Action_Name(控制器方法)”所指向的Action。由于这里并没有指定“Controller”的名字，所以会指向默认(该页面所属)的控制器。
　　`@Html.ActionLink("链接文字","Action_Name","Controller_Name")`：如要将链接指向其他控制器(不是当前页面所属)时，应该在第三参数中注明控制器的名称。
　　`@Html.ActionLink("链接文字","Action_Name",new {id = 12,page = 5})`：当链接需要额外的路由参数时，可以传入匿名类型的数据。比如这里会生成这样的标签：`<a href="/Controller_Name/Action_Name?id=12&page=5">链接文字</a>`
　　`@Html.ActionLink("链接文字","Action_Name",null,new {@class = "btnLink"})`：当要在生成的HTML标签中加入额外的HTML属性时，可以在第四个参数中加入。比如设置HTML的class属性。但是由于class是c#中的关键字，所以在 这里使用时需要在前面加上@符号；而且当需要输出带有“-”数属性时(HTML5的属性)，比如“data-value”，需要将“-”换成“_”。

　　**使用Html.ActionLink()时，第一个参数不能输入空字符串或者null值，否则就会抛出异常。**

# 2. 表单提交：Html.BeginForm
　　`@Html.BeginForm()`可以生成表单提交标签。表单的提交方式默认为Post，可以使用参数将提交方式修改为GET。
　　
Html.BeginForm()的两种使用方法：
1. 使用`@using{}`包裹住Html.BeginForm()以及表单中的标签成员

```csharp
@using(Html.BeginForm())
{
    <p>账号：@html.TextBox("Account")</p>
    <p>密码：@Html.PassWord("PassWord")</p>
    <input type="submit" value="注册" />
}
```

1. 在开头和结尾分别使用`Html.BeginForm()`、`Html.EndForm()`

```html
@{Html.BeginForm("About","Home");}
    @Html.TextArea("Date")
    @Html.TextArea("123")
    <input type="submit" />
@{Html.EndForm();}
```

　　**推荐使用using的方式编写Html.BeginForm(),因为代码比较可看。**

# 3. 使用GET方式提交表单
　　如果要使用GET方式提交表单，可以输入第三个参数`FormMethod.Get`：

```html
@using(Html.BeginForm("Search","Home",FormMethod.Get))
{
    @Html.TextArea("2333")
    <input type="submit" />
}
```

# 4. 使用上传(提交)文件功能
　　想要实现上传文件的功能，需要在`<form>`表单中增加一个`enctype`属性，所以在Html.BeginForm()中需要添加第四个参数：
　　因为Html辅助方法中没有File方法，所以需要借助TextBox方法来代替，然后给TextBox传入第三个参数将type属性修改为file。

```html
@using(Html.BeginForm("Upload","File",FormMethod.Post,new {enctype = "multipart/from-data"}))
{
    @Html.TextBox("FileName","",new {type="file",size="10"})
    <input type="submit" />
}

<!--输出的HTML代码为：-->
<form action="/File/Upload" enctype="multipart/from-data" method="post">
    <input id="FileName" name="FileName" size="10" type="file" value="" />
    <input type="submit" />
</form>
```

# 5. 常用的表单输入辅助方法
　　● `Html.BeginForm()`：输出`<form>`标签
　　● `Html.EndForm()`：输出`</form>`标签
　　● `Html.Lable()`：输出`<lable>`标签
　　● `Html.TextBox()`：输出`<input type="text" />`标签
　　● `Html.TextArea()`：输出`<textarea>`标签，可以指定rows和column的值。
　　● `Html.PassWord()`：输出`<input type="password" />`标签
　　● `Html.Checkbox()`：输出`<input type="checkbox" />`标签
　　● `Html.RadioButton()`：输出`<input type="radio" />`标签，可以指定是否要默认选取预设值`@Html.ReadioButton("name","vlaue",true)`。
　　● `Html.DropDownList()`：输出`<select>`标签
　　● `Html.ListBox()`：输出`<select mulitiple>`标签
　　● `Html.Hidden()`：输出`<input type="hidden"/>`标签
　　● `Html.ValidationSummary()`：输出表单验证失败是的错误信息摘要

　　这些标签大多都有重载，比如`@Html.TextBox()`重载：

```html
@Html.TextBox("UserName","Is2",new{id="textId"})
<!--输出下面的HTML代码：-->
<input id="textId" name="UserName" type="text" value="Is2" />
```

# 6. 强类型的辅助方法
　　强类型的辅助方法也就是可以在辅助方法中使用@model加载需要的内容，而且可以使用简洁的lambda表达式。
　　强类型的辅助方法名称基本上就是“原本的名字后面加上For”，比如`@Html.TextBox()`的强类型形式就是`@Html.TextBoxFor()`。
　　要使用强类型的辅助方法，需要在View最开始注明强类型模式:`@model MvcApplicationTest.Product`。

## 6.1. 使用强类型辅助方法的例子
**数据模型：**

```csharp
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

namespace MvcApplicationTest
{
    public class Product
    {
        public int Id{ get; set; }
        [Required]
        [DisplayName("产品名称")]
        public string Name{ get; set; }
        [Required]
        [DisplayName("产品说明")]
        public string Description{ get; set; }
        [Required]
        public int UnitPrice{ get; set; }
    }
}
```

**View：**

```html
@model MvcApplicationTest.Product

@using(Html.BeginForm())
{
    @Html.ValidationSummary(true)
    
    <fieldset>
        <h2>产品信息</h2>
        <div>
            @Html.LableFor(model=>model.Name)
        </div>
        <div>
            @Html.LableFor(model=>model.Description)
        </div>
        <input type="submit" />
    </fieldset>
}
```

## 6.2. 使用辅助方法加载分布视图
　　使用`@Html.Partial()`方法可以在View中加载一个分布视图(Partial View)。
　　因为分布视图是片段式的，所以需要在一个完整页面中加载分布视图。如果分布视图中需要参数，也可以在方法中传入。

**index.cshtml**

```csharp
@model MvcApplicationTest.Product

@Html.Partial("ProductInfo",(object)model.Name)
```

**Partial View(ProductInfo)**

```csharp
@model System.String

@Html.TextBox(model)
```
