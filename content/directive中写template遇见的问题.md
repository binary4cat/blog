---
title: directive中写template遇见的问题
date: 2015-07-18T23:51:57+08:00
tags: ["AngularJS"]
categories: ["Frontend"]
permalink: Write-the-template-directive-met-problems
description: directive中写template遇见的问题
---
　　记录一下先前在写指令时，遇到的一个令人无语的错误，避免以后重犯。

　　错误的代码：
```
app. directive( ' ckqlnfo' ,[function(){
        return {
    　　　restrict: ' AE ' 
    　　　replace: true, 
    　　　template : '<p>模板</p><p>模板</p> ' 
        ｝
    ｝
```
<!--more-->
　　首先看看指令中template的值`<p>模板</p><p>模板</p>`，这段看起来并没有什么错，但是在实际运行中会造成程序抛出异常，因为这段html代码有两个**p**节点，在Angular解析这个指令的时候，不知道应该解析template所标注的哪一个**p**节点。

正确的代码：
```
app. directive( ' ckqlnfo' ,[function(){
        return {
    　　　restrict: ' AE ' 
    　　　replace: true, 
    　　　template : '<div><p>模板</p><p>模板</p></div>' 
        ｝
    ｝
```
　　这段代码中我们为template模板代码中添加了一对`div`标签，这样就能被Angular正确的解析了，因为模板有了一个明确的渲染开始和结束。


**温馨提示**：
　　上面的指令我们使用了`template`直接以字符串的形式编写模板，有时候我们的模板代码较多，会使用`templateUrl`引用一个新的html模板文件。

看下面的两个模板代码：
*模板A*：
```
<!--这是模板A的注释-->
<div>
    <p>模板</p>
    <p>模板</p>
</div>
```
*模板B*：
```
<div>
    <!--这是模板B的注释-->
    <p>模板</p>
    <p>模板</p>
</div>
```

**告诉你们真相**：注释也是一个节点元素，也会被解析……
别问我怎么知道的，也别问我静静是谁...![](/image/face/捂脸哭.png)
