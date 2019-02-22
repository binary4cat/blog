---
title: SQL Server表连接(Join)
date: 2015-04-20T21:44:19+08:00
tags: ["SQL Server"]
categories: ["Database"]
permalink: SQL-Server-Join
description: SQL Server表连接(Join)
---
　　Join用于根据两个或多个表之间的关系，从这些表之间查找数据。
　　有时候我们为了方便管理会将业务分拆成多个表，表之间用“键(key)”连接，之间存在一定的关联，比如我们有会员表专门存储会员信息，有订单表存储订单信息，但是我们要知道那些会员订购了那些商品，就需要结合两张表去查找这些信息了。
　　
　　Join有四种用法：
　　1. inner join :内连接。能够找到两个或多个表中建立连接字段值相等的记录。没有匹配的行就不会列出。
　　2. left join :左连接。关键字前面是左表，后面是右表。--左连接可以得到左表所有数据，即使右表没有匹配的行。如果建立关联的字段值在右表中不存在，那么右表的数据就以null值替换。
　　3. right join :右连接。关键字前面的表是左表，后面的表是右表。--右连接可以得到右表所有数据，即使左表没有匹配的行。如果建立关联的字段值在右左表中不存在，那么左表的数据就以null值替换。
　　4. full join :可以得到左右连接的综合结果--去重复
<!--more-->
# 语法

```sql
SELECT column_name(s)
FROM table_name1
inner|left|right|full JOIN table_name2 
ON table_name1.column_name=table_name2.column_name
```

有两张表：
![](http://ww1.sinaimg.cn/mw690/c55a7aeejw1f1e9ogltfcj20ha05wmx1.jpg)
![](http://ww1.sinaimg.cn/mw690/c55a7aeejw1f1eagmfb0cj20fe04cjr8.jpg)
我们现在要获取谁订购了什么商品的数据：

# inner Join代码示例
```sql
--我们可以用这个种方式获取
select person.Name,person.Address,orders.ordernum 
from person,orders 
where person.P_Id=orders.P_Id
```
![](http://ww2.sinaimg.cn/mw690/c55a7aeejw1f1e9stu5clj20ex04ijr6.jpg)
**使用Join实现：**
```sql
select person.Name,person.Address,orders.ordernum 
from person 
inner join orders 
on person.P_Id=orders.P_Id
```
![](http://ww2.sinaimg.cn/mw690/c55a7aeejw1f1e9stu5clj20ex04ijr6.jpg)

# left join代码示例
```sql
select person.Name,person.Address,orders.ordernum 
from person 
left join orders 
on person.P_Id=orders.P_Id
```
![](http://ww1.sinaimg.cn/mw690/c55a7aeejw1f1easqr69jj20h404mq3a.jpg)

# right join代码示例
```sql
select person.Name,person.Address,orders.ordernum 
from person 
right join orders 
on person.P_Id=orders.P_Id
```
![](http://ww1.sinaimg.cn/mw690/c55a7aeejw1f1eayxaeauj20ep04ymxd.jpg)

# full join代码示例
```sql
select person.Name,person.Address,orders.ordernum 
from person 
full join orders 
on person.P_Id=orders.P_Id
```
![](http://ww1.sinaimg.cn/mw690/c55a7aeejw1f1eb2147ruj20fz05ft8z.jpg)
