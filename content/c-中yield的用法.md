---
title: 'c#中yield的用法'
date: 2016-03-26T17:01:24+08:00
tags: ["c#基础知识"]
categories: ["cSharp"]
permalink: Usage-of-yield-in-c-#
description: c#中yield的用法
---
　　记录一下c#中的一个关键字`yield`的用法，之前基本没怎么用过这个算是语法糖的关键字，最近做了一个小的机构账号管理项目，读取DataReader对象数据时，每一次都得Add到集合中，记得以前见过有人用过这个方法，写起来很方便，所以学习了一下，记录下来以备查看。

### 之前查找数据库数据的部分代码
```
public List<Organization> OrgModel=new List<Organization>();
string sqlText="select * from Organization";
var dataTable = SqlHelper.ExecuteReader(sqlText);
            if (dataTable.HasRows)
            {
                while (dataTable.Read())
                {
                    Organization org = new Organization();
                    org.org_code = dataTable["org_code"].ToString();
                    org.org_name = dataTable["org_name"].ToString();
                    org.org_acount = dataTable["org_acount"].ToString();
                    org.org_password = dataTable["org_password"].ToString();
                    OrgModel.Add(org);
                }
            }
            return orgModel;
```
<!--more-->
### yield的方式
　　　使用`yield`时，yield会返回结果集合，比如下面的代码，返回的是一个`IEnumerable<org>`，类型的数据，所以在组织数据的时候，需要是符合`IEnumerable`、`IEnumerable<T>`、`IEnumerator`或 `IEnumerator<T>`的数据。
```
//public List<Organization> OrgModel=new List<Organization>();
string sqlText="select * from Organization";
var dataTable = SqlHelper.ExecuteReader(sqlText);
            if (dataTable.HasRows)
            {
                while (dataTable.Read())
                {
                    Organization org = new Organization();
                    org.org_code = dataTable["org_code"].ToString();
                    org.org_name = dataTable["org_name"].ToString();
                    org.org_acount = dataTable["org_acount"].ToString();
                    org.org_password = dataTable["org_password"].ToString();
                    //OrgModel.Add(org);
                    yield return org;
                }
            }
            //因为yield会返回集合数据，所以这里不需要再return了
            //return orgModel;
```


### 一个小例子演示yield的用法
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace yield
{
    class Program
    {
        static void Main(string[] args)
        {
            //输出该数组中的奇数
            int[] ints = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 0 };

            //用到该返回值的时候，才去执行含有yield的方法
            //在下面的foreach没有执行时，num是没有值的。
            var num = GetOdd(ints);

            //使用foreach遍历，因为返回的是符合条件的可枚举对象
            foreach (var item in num)
            {
                Console.WriteLine(item);
            }
            
            Console.ReadKey();
        }

        //返回值需要是IEnumerable类型
        private static IEnumerable<int> GetOdd(int[] ints)
        {
            for (int i = 0; i < ints.Length; i++)
            {
                if (i%2!=0)
                {
                    yield return i;
                }
            }
        }
    }
}
```
执行结果：
![](http://ww1.sinaimg.cn/mw690/c55a7aeegw1f2adypz1s8j20rp0g7wfd.jpg)
