---
title: 使用CsvHelper导入导出csv文件
photos: 
date: 2017-02-25T00:39:03+08:00
categories: ["c#"]
tags: ["ASP.NET","ASP.NET MVC"]
permalink: Use-CsvHelper-ReadOrWrite-csvFile
description: 使用CsvHelper导入导出csv文件
---
> 今天记录一下使用[CsvHelper](https://github.com/JoshClose/CsvHelper)导入导出CSV文件的方法。


## CSV介绍
　　csv文件是一种纯文本存储的文件格式，行和列分别用逗号和换行来代替，可以直接使用Excel打开。在代码中可以直接使用`TextWrite` 操作，比较方便快速。

## CsvHelper

### 项目介绍：
　　github主页：[https://github.com/JoshClose/CsvHelper](https://github.com/JoshClose/CsvHelper)
　　文档：[http://joshclose.github.io/CsvHelper/](http://joshclose.github.io/CsvHelper/)
　　wiki：[https://github.com/JoshClose/CsvHelper/wiki](https://github.com/JoshClose/CsvHelper/wiki)
<!--more-->
### Examples
模拟场景是需要从数据库中导出导入用户信息，有如下一个类：
```cSharp
class People{
	public string Name{get;set;}
	public int Age{get;set}
	public string Gender{get;set;}
}
```
　　
#### Write
写入的时候需要注意列标题的名字问题，因为CsvHelper会默认将属性名作为列标题，想显示中文就得做映射([参考文档](https://github.com/JoshClose/CsvHelper/wiki/Fluent-Class-Mapping))
Map类：
```cSharp
/// <summary>
/// 配置导出类映射
/// </summary>
sealed class PeopleMap:CsvClassMap<People>
{
    public PeopleMap()
    {
        Map(m => m.Name).Name("姓名");
        Map(m => m.Age).Name("年龄");
        Map(m => m.Gender).Name("性别");
    }
}
```
上面的映射类可以根据需要调整，可以配置属性输出的列索引等，可以参考项目[wiki](https://github.com/JoshClose/CsvHelper/wiki/Fluent-Class-Mapping)
写入操作代码：
```cSharp
// 代替数据库读取操作
var peoples= new List<People>();
for (int i = 0; i < 100000; i++)
{
    peoples.Add(new People()
    {
        Name = "iCtr",
        Age = 100,
        Gender = "未知"
    });
}
//测试写入操作的时间
Stopwatch watch = new Stopwatch();
watch.Start();
using (TextWriter write = new StreamWriter(@"C:\Users\ictr-jd.hai\Desktop\Test.csv", false, Encoding.UTF8))
{
     using (var csv = new CsvWriter(write))
     {
          csv.Configuration.Encoding = Encoding.UTF8;
          csv.Configuration.RegisterClassMap<PeopleMap>();  //映射
          csv.WriteHeader<People>(); //写入列标题
          //循环写入行
          foreach (var item in peoples)
          {
              csv.WriteRecord(item);
          }
     }
}
watch.Stop();
Console.WriteLine(watch.ElapsedMilliseconds.ToString());
Console.ReadKey();
```

#### Read
读取操作代码：
```cSharp
var objs = new List<People>();
//测试读取的速度
Stopwatch watch = new Stopwatch();
watch.Start();
using (TextReader reader=new StreamReader(@"C:\Users\ictr-jd.hai\Desktop\Test.csv",Encoding.UTF8))
{
     using (var csv=new CsvReader(reader))
     {
          csv.Configuration.Encoding = Encoding.UTF8;
          //csv.Configuration.RegisterClassMap<PeopleMap>();  //如果有标题头，那么也需要映射，而且字段名字必须匹配
          //csv.Configuration.HasHeaderRecord = false;        //如果没有标题头，那么就配置为false
          //一次行读取全部的行，也可以使用循环一行行读取(多种读取方式请参考文档)
          objs = csv.GetRecords<People>().ToList();
     }
}
Console.WriteLine(objs.Count);
watch.Stop();
Console.WriteLine(watch.ElapsedMilliseconds.ToString());
Console.ReadKey();
```

## 结束
　　操作csv文件比较简单，就是读取一个文本文件的操作，借助CsvHelper的帮助，可以省去我们自己排版、映射类等操作，操作速度很快，其配置也多种多样，文档比较详细，推荐大家使用，在此感谢组件作者的工作。
