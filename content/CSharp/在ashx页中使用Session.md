---
title: 在ashx页中使用Session
date: 2016-03-20T23:30:19+08:00
tags: ["WebForm"]
categories: ["ASP.NET"]
permalink: Ashx-pages-use-Session
description: 在ashx页中使用Session
---
　　
### 正文
　　在ashx中使用Session不能直接`context.Session["sessionName"]=value;` ,需要引入一个命名空间和继承一个接口。
　　命名空间：`using System.Web.SessionState;`  作用：“IRequiresSessionState”接口的命名空间
　　接口：`IRequiresSessionState`    作用:指定目标 HTTP 处理程序需要对会话状态值具有读写访问权<!--more-->

　　`IRequiresSessionState`：这是一个**标记接口**，没有任何方法。指定目标 HTTP 处理程序需要对会话状态值具有读写访问权。
