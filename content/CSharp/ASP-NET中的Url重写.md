---
title: ASP.NET中的Url重写
date: 2015-02-11T13:47:48+08:00
tags: ["ASP.NET","转载"]
permalink: ASP-NET-Url-Rewriting
categories: ["cSharp"]
---
##  一. 为了页面更有利于seo优化，url重写程序需要做出的不可缺少的手段之一。

## 二.目前url重写最常见的两种方法有：
　　1. 通过熟悉页面的内部执行流程，也就是asp.net请求管道中的相关知识点，进行url重写。
　　2. 通过使用微软提供的UrlRewriter.dll实现url重写（简单）；

## 三、在请求管道中的第一个请求管道中拦截url并使其完成url重写功能：
　　1. 熟悉请求管道实现程序运行的全过程：
　　　　(1)：BeginRequest: 开始处理请求
　　　　(2)：AuthenticateRequest授权验证请求，获取用户授权信息
　　　　(3):PostAuthenticateRequest获取成功
　　　　(4): AunthorizeRequest 授权，一般来检查用户是否获得权限   <!--more-->
　　　　(5):PostAuthorizeRequest:获得授权
　　　　(6):ResolveRequestCache:获取页面缓存结果
　　　　(7):PostResolveRequestCache 已获取缓存
　　　　(8):PostMapRequestHandler 创建页面对象
　　　　(9):AcquireRequestState 获取Session-----先判断当前页面对象是否实现了IRequiresSessionState接口，如果实现了，则从浏览器发来的请求报文体中获得SessionID,并到服务器的Session池中获得对应的Session对象，最后赋值给HttpContext的Session属性
　　　　(10)PostAcquireRequestState 获得Session
　　　　(11)PreRequestHandlerExecute:准备执行页面对象执行页面对象的ProcessRequest方法
　　　　(12)PostRequestHandlerExecute 执行完页面对象了
　　　　(13)ReleaseRequestState 释放请求状态
　　　　(14)PostReleaseRequestState 已释放请求状态
　　　　(15)UpdateRequestCache 更新缓存
　　　　(16)PostUpdateRequestCache 已更新缓存
　　　　(17)LogRequest 日志记录
　　　　(18)PostLogRequest 已完成日志
　　　　(19)EndRequest 完成
![](http://ww1.sinaimg.cn/mw690/c55a7aeejw1f1hc5019pej20it0buwhk.jpg)
　　2. 直接上代码：
```
using System;
using System.Collections.Generic;
using System.Web;
using System.Text;
using System.Text.RegularExpressions;

namespace UrlReWriter
{
    class UrlWriter:IHttpModule
    {
        #region IHttpModule 成员

     
        public void Init(HttpApplication context)
        {
           context.BeginRequest+=new EventHandler(context_BeginRequest);
        }

        void context_BeginRequest(object sender, EventArgs e)
        {
            //获得当前页面请求管道的HttpApplication对象
            HttpApplication application = sender as HttpApplication;
            HttpContext context = application.Context;//获得上下文对象
            string url = context.Request.Url.LocalPath;//获得URL(不包含域名和路径)

            Regex reg1 = new Regex("/WebForm2/[A-Z0-9a-z_]+");//注意文件夹与文件名称的大小写.
            if (reg1.IsMatch(url)) //这种情况是匹配根据ID值来查看某个文章的内容。
            {
                string strUserName = url.Substring(url.LastIndexOf('/')+1);
                context.RewritePath("/WebForm2.aspx?id=" + strUserName); //前台的列表页就可以这样写:<a href='../WebForm2/<%#Eval("id")%>'></a>
            }
            else if (url.Contains("/admin"))   //如果url中包含"/admin"字样则转向，实现用户验证，哈哈，不过这里是错的，应为这个请求管道中还获取不到session值呢，这个验证最好在第九个请求管道（AcquireRequestState ）中完成。
            {
                context.RewritePath("/Error.aspx"); 
            }
        }
        public void Dispose()
        {
            
        }

        #endregion
    }
}
```
```
<%@ Page Language="C#" AutoEventWireup="true" CodeBehind="WebForm1.aspx.cs" Inherits="url重写.WebForm1" %>

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

<html xmlns="http://www.w3.org/1999/xhtml">
<head runat="server">
    <title></title>
</head>
<body>
    <form id="form1" runat="server">
    <div>
       <a href="WebForm2/12">重写</a>   <!--这里是有利于seo的链接-->
    </div>
    </form>
</body>
</html>
```
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;

namespace url重写
{
    public partial class WebForm2 : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
            Response.Write(Request.QueryString["id"]);           //在转到的页面输出传过来的参数
        }
    }
}
```
```
 <httpModules>
      <add name="UrlReWriter" type="UrlReWriter.UrlWriter,UrlReWriter"/>
    </httpModules>
    <pages controlRenderingCompatibilityVersion="3.5" clientIDMode="AutoID"/>
  </system.web>
```
**Note:**记得在url重写项目中引用UrlReWriter项目。否则httpModules接口没有注册，url重写泡汤是必然的。

原文来自[博客园](http://www.cnblogs.com/knowledgesea/archive/2012/10/08/2715350.html)
作者：**张龙豪**
