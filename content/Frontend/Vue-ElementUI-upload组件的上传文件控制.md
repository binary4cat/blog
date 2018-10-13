---
title: Vue-ElementUI upload组件的上传文件控制
date: 2018-06-14T10:06:13+08:00
categories: ["Frontend"]
tags: ["Vue"]
permalink: Vue-ElementUI-upload-file-controller
description: Vue-ElementUI upload组件的上传文件控制，上传失败文件的复原，作为表单时的填充
photos: https://cdn-images-1.medium.com/max/1003/1*M20GkaLW-OBcz6-ifDAJEg.png
---
<!-- TOC -->

- [1. 上传失败文件恢复](#1-上传失败文件恢复)
- [2. 复原文件上传成功表单](#2-复原文件上传成功表单)

<!-- /TOC -->
# 1. 上传失败文件恢复
element的上传组件有很多需要自行控制的东西，此次遇到的问题是怎么在上传失败后，保持选择的文件的不变，可以再次尝试上传。

首先我们有一个事件，是上传失败后的回调事件：
```javascript
/**
   * 文件上传失败后的事件
   */
  uploadErrorFunc(err, file, fileList){
      console.log(file)
  }
```
<!--more-->
这个事件会在每个文件上传失败后都进行调用，我们需要保持选择文件的不变，需要在这里做文章。
在分析之前，我们先看看选择的文件有什么值得注意的数据，这里我们还需要写一个文件选择(改变的事件)：
```javascript
/**
   * 文件列表改变事件
   */
  onChange(file, fileList) {
    console.log(file)
  }
```
这个function会在选择每个文件的时候都触发一次，我们打印一下选择的文件信息看看有什么数据：
![](/image/Frontend/2018-6-14-1.jpg)
比较重要的就是红框中标出的数据，都是文件的信息。

然后我们让文件上传失败，看看失败后回传的文件信息有哪些数据：
![](/image/Frontend/2018-6-14-2.jpg)

在上面两组数据中，我们需要注意的是`status`这个属性，可以看到，选择文件的时候值为`ready`，文件上传失败后回传的文件的值为`fail`，同一个文件有两种状态，我们这时候就大概能知道UI组件的处理方式了。

我们把错误回调事件中拿到的file的status属性的值改为"ready"就可以了。

接下来，我们把this.$refs.upload打印出来：
![](/image/Frontend/2018-6-14-3.jpg)
数据很多，我们关注一个主要的属性，uploadFiles，从名字就可看出来，这是需要上传的文件的列表数据，很容易就想到处理的办法了，在错误发生时，拿到回调的file数据，并且把file中的status属性的值改为预备状态"ready"，然后push到upload组件的uploadFiles数组中。
```javascript
 /**
   * 文件上传失败后的事件
   */
  uploadErrorFunc(err, file, fileList){
      file.status="ready"
      this.$refs.upload.uploadFiles.push(file);
  }
```


# 2. 复原文件上传成功表单
有时候我们的上传组件在一个form表单中，在更新表单的时候，我们需要让上次上传的图片恢复成上传成功的样子，那么也可以使用上面的方式进行编写。

也就是更新表单的时候，让上传过的图片出现在上传组件中，同样是给`this.$refs.upload.uploadFiles`赋值，只不过这时的`status`变成了`success`。需要注意的是，这时的url需要是一个可以外网访问到的图片，也就是图片的完整链接。
```javascript
this.$refs.upload.uploadFiles=[{
    status:"success",
    url:imgUrl  //赋值返回的图片完整链接
}]
```
