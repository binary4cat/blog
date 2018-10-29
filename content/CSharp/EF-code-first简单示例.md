---
title: EF code first简单示例
date: 2016-04-13T20:35:53+08:00
tags: ["Asp.Net MVC","EntityFramework"]
permalink: EF-code-first-simple-example
categories: ["cSharp"]
---
　　一个简单的MVC5+EF6小demo，演示mvc和ef的简单编写流程。

## 创建项目
　　1、选择.Net Farmework版本为4.5以上的，这里我选择4.5.2
　　2、选择模板，选择**Empty**，勾选MVC引用：![](http://ww1.sinaimg.cn/mw690/c55a7aeegw1f2v56p11fvj20m80h8tc1.jpg)<!--more-->
　　可以看到VS自动帮我们添加了asp.net MVC默认约定的文件夹，按照asp.net MVC的约定大于配置的原则，不要随意改变文件夹的名字，也不要将文件随意添加，不然可能导致程序出错。![](http://ww2.sinaimg.cn/mw690/c55a7aeegw1f2v5ax49q3j207x0ec74j.jpg)
　　关于基本项目文件的说明：
　　`App_Start`：一些全局的配置类，配合`Global`使用。
　　`Controllers`：存放MVC控制器的文件夹。
　　`Models`：存放数据模型(class)文件夹。
　　`Views`：存放视图的文件夹。

## 添加EF组件
　　EntityFarmework是作为组件的形式存在的，要使用EntityFarmework需要添加它的引用。
　　1、使用Nuget添加EF：右击引用==>管理NuGet程序包，会弹出NuGet管理界面。
　　2、点击“浏览”按钮，可以看到“EntityFramework”作为常用组件会排列在第一位，选择后，点击右边的“安装”按钮即可进行安装。![](http://ww4.sinaimg.cn/mw690/c55a7aeegw1f2v5pqpkftj218g0oeq68.jpg)

## 添加User数据模型，并且使用code first自动生成数据库
　　1、在“Models”文件夹中添加一个名为`User`的类，并添加以下内容：
```
public int Id { get; set; }

[Required(ErrorMessage = "用户名必填")]
[Display(Name = "用户名")]
public string Name { get; set; }

[Required(ErrorMessage="密码必填")]
[Display(Name="密码")]
public string Pwd { get; set; }
```
　　注：需要添加一个命名空间`System.ComponentModel.DataAnnotations`，代码中的特性标签是作为数据注解使用的，关于数据注解，在下一篇中总结。

　　2、添加数据库上下文类：
　　在“Models”文件夹中添加一个名为`DbEntities`的类作为数据库上下文类，该类需要继承`DbContext`类。然后添加如下代码：
```
public class DbEntities:DbContext
{
    //base中的字符串为web.config中设置的数据库连接字符串名
    public DbEntities():base("name=SQLConnString")
    {
        //表示创建数据库的方式
        Database.SetInitializer<DbEntities>(new CreateDatabaseIfNotExists<DbEntities>());
    }
    //存储User数据的属性
    public DbSet<User> User { get; set; }
}
```
　　3、配置数据库连接字符串
　　打开“web.config”文件，添加数据库连接字符串，比如：
　　因为目前为止还没有创建数据库，所以这里指定数据库名称时，可以随意起一个不重复的名字即可，创建时，就会自动按照该名称创建数据库。
　　同时注意，连接字符串的`name`属性的值和数据库上下文中`base`的值要一致。
```
<connectionStrings>
  <add name="SQLConnString" providerName="System.Data.SqlClient" connectionString="Data Source=.;Initial Catalog=MVC5_Test;Persist Security Info=True;User ID=sa;Password=sa" />
</connectionStrings>
```
　　4、使用EF数据迁移创建数据库
　　依次点击：工具==>NuGet包管理器==>程序包管理控制台，打开power shell窗口。
　　输入：
　　　　`Enable-Migrations`：表示开启该项目的数据迁移功能，只需运行依次，以后迁移数据时，不需要再运行该命令。
　　　　`Add-Migration migrationName`：添加迁移命令，需要给本次迁移起一个名字(如migrationName)，这时还没有开始迁移，只是打上了迁移的标记。
　　　　`Update-Database`：将迁移更新到数据库中。
　　三条命令全部成功执行后，不出意外的话，数据库中应该已经创建了相应的数据库、表、字段：![](http://ww4.sinaimg.cn/mw690/c55a7aeegw1f2v6lnz9r9j20at0823ym.jpg)
　　同时，项目中也为我们自动添加了一个名为“Migrations”的文件夹，里面有两个文件，分别是数据迁移的配置类(Configuration)、数据迁移的内容(xxxxx_migrationName)。

## 编写简单的登录页面
　　我们可以直接在数据库中添加一条用户登录的数据，Name=admin,Pwd=123456
　　1、添加控制器：
　　　　在“Controllers”文件夹右击，添加==>控制器，在弹出的窗口中选择“MVC5控制器 空”，然后起名为`HomeController`。
　　2、添加首页视图：
　　　　可以看到，自动为我们生成了一个`Index`方法，光标停在`Index`上面，然后鼠标右击，在弹出的菜单中选择“添加视图”，然后在弹出窗口中不修改视图的名字(按照约定，视图的名字必须和控制器方法名一致)，去除“使用布局页”前面的对勾(关于如何使用布局页将在另一篇博客中总结)，点击添加。
　　3、在Index视图中添加表单：
　　　　VS帮我们生成了一个基础的Razor模板，我们在`<body></body>`中添加表单内容：
　　关于@html辅助方法，在前面的[博客](http://www.hais2.com/2015/08/02/HTML%E8%BE%85%E5%8A%A9%E6%96%B9%E6%B3%95/)中简单总结了一下。
　　在页面开头我们还需要引入在Razor中使用的数据模型的完全限定名，使用`@model`引入，比如`@model Mvc5_EF6_Demo.Models.User`
```
@using (Html.BeginForm("Index", "Home"))
{
       @Html.ValidationSummary(true)
       <h1>请输入用户名密码登陆</h1>
       @Html.LabelFor(model => model.Name)
       @Html.TextBoxFor(model => model.Name)
       @Html.ValidationMessageFor(model => model.Name)
       <br />
       @Html.LabelFor(model => model.Pwd)
       @Html.TextBoxFor(model => model.Pwd)
       @Html.ValidationMessageFor(model => model.Pwd)
       <br />
       <input type="submit" value="登陆" />
}
```
　　4、添加登录事件处理方法：
　　在`HomeController`控制器中创建一个名为`Index`的方法，因为表单的发送地址执行了Index方法，但是现在又两个Index方法，这是程序不允许的，需要进行处理。
　　由于表单是post请求发送的，在接收表单的Index方法上面写上一个特性标签`[HttpPost]`，表示该方法只响应post请求，然后接收参数为`User`类型的一个参数，代码如下：
```
        [HttpPost]
        public ActionResult Index(User user)//将表单中的输入自动组成User类，传递到Login方法
        {
            //判断表单模型是否验证成功
            if (!ModelState.IsValid)
            {
                return View(user);
            }

            //如果表单验证成功
            //创建数据上下文类对象
            DbEntities db = new DbEntities();
            //查找User表中的所有数据,转换成List类型
            var userInfo = db.User.Select(s => s).ToList();
            //简单的判断一下输入和数据库是否相同
            if (userInfo[0].Name==user.Name&&userInfo[0].Pwd==user.Pwd)
            {
                //跳转到登录成功的Action
                return RedirectToAction("LoginOk");
            }
            return View(user);
        }
```
　　5、添加登录成功视图：
　　在`HomeController`控制器中增加一个`LoginOk`方法并且添加视图，用来展示登录成功页面

## 测试
　　启动项目，测试：
　　1、我们少填一个字段：
　　![](http://ww2.sinaimg.cn/mw690/c55a7aeegw1f2v8j7u70cj20dd07ggm1.jpg)
　　可以看到，不填写的话，就会显示相应的提示，因为我们在数据模型中添加了`[Required]`特性标签，就是必填的意思，然后在提交到Action中时，使用`ModelState.IsValid`进行判断。
　　2、验证成功：
　　![](http://ww1.sinaimg.cn/mw690/c55a7aeegw1f2v8ojeym7j20g70610sv.jpg)
　　3、验证失败：
　　暂时没有验证，所以会跳转到Index页面。

[代码下载](http://pan.baidu.com/s/1boCP2RP)
