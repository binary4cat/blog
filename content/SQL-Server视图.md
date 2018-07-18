title: SQL Server视图
date: 2015-04-27T16:57:47+08:00
tags: SQL Server
categories: SQL
permalink: SQL-Server-View
description: SQL Server视图
---
## 什么是视图
　　视图就是一张虚拟表，是基于SQL语句的**结果集**的可视化的表，视图中的字段是来自一个或多个表中的字段(相当于子查询的结果集那样)。
　　视图简单的理解为一个统一访问数据的接口，用户只需要访问这个接口，而不需要知道具体的表的结构和表之间的关系。
　　数据库的结构和设计不会受到视图中的函数、where、join语句的影响。

## [视图的优点和使用规则](http://www.cnblogs.com/CareySon/archive/2011/12/07/2279522.html)
### 为什么要使用视图
•   视图隐藏了底层的表结构，简化了数据访问操作 
•   因为隐藏了底层的表结构，所以大大加强了安全性，用户只能看到视图提供的数据 
•   使用视图，方便了权限管理，让用户对视图有权限而不是对底层表有权限进一步加强了安全性 
•   视图提供了一个用户访问的接口，当底层表改变后，改变视图的语句来进行适应，使已经建立在这个视图上客户端程序不受影响 
<!--more-->
### 规则
•   在View中，除非有TOP关键字，否则不能用Order By子句（如果你一意孤行要用Order by,这里有个hack是使用Top 100 percent…..) 
•   View在每个Schema中命名必须独一无二 
•   View嵌套不能超过32层（其实实际工作中谁嵌套超过两层就要被打PP了-.-) 
•   Compute,compute by,INTO关键字不允许出现在View中 
•   View不能建立在临时表上 
•   View不能对全文索引进行查询 


## 编写视图

### 语法
create view vw_name
as
　　查询命令


### 示例代码
```
if exists(select * from sysobjects where name='vw_GetStaffInfo')
    drop view vw_GetStaffInfo

create view vw_GetStaffInfo
as
    select Name,Age,ClassId from Staff where ClassId='.NET'
    
    
--调用视图，和使用表的方法一样
select * from vw_GetStaffInfo
```