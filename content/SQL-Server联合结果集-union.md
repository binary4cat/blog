title: SQL Server联合结果集(union)
date: 2015-04-10T20:46:09+08:00
tags: SQL Server
categories: SQL
permalink: SQL-Server-union
description: SQL Server联合结果集(union)
---
　　`union`操作符用于合并另个或多个select(查询)语句的结果集。
　　在使用`union`操作符时，要注意联合的select语句需要有相同的列、相似的数据类型、列的顺序要相同。

### union和union all
★ `union`默认去掉合并结果中重复的数据。
★ `union all`可以保留重复的结果数据。

### 语法
select * from table1_name
union|union all
select * from table2_name

**union结果集中的列名总是等于第一条select语句的列名**
<!--more-->
### 代码示例
![](http://ww3.sinaimg.cn/mw690/c55a7aeejw1f1e7gllxakj20hw06ot98.jpg)
![](http://ww2.sinaimg.cn/mw690/c55a7aeejw1f1e7hszpdjj20h505qmx0.jpg)
**union示例：**
```
select Name from Staff
union
select name from Staff2
```
![](http://ww1.sinaimg.cn/mw690/c55a7aeejw1f1e7krrdlxj20j808yglj.jpg)
**union all示例：**
```
select Name from Staff
union all
select name from Staff2
```
![](http://ww2.sinaimg.cn/mw690/c55a7aeejw1f1e7m2hi95j20k00a4t8o.jpg)