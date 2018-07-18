---
title: bootstrap-fileinput组件在上传时传递额外参数
date: 2016-11-23T20:54:46+08:00
tags: 前端组件
categories: Frontend
permalink: bootstrap-fileinput-uploadExtraData
description: bootstrap-fileinput组件在上传时传递额外参数
photos:
- http://ww4.sinaimg.cn/large/c55a7aeejw1fa2eocj9c4j20dc07ijrg.jpg
---
> 该问题的使用场景是：在微信平台素材上传的页面使用，在上传时需要发送一个参数，让后台判断是临时素材还是永久素材。 但是该组件在一开始就已经初始化(问题所在)了要发送的参数，也就是说没有办法后期追加额外的参数。

　　由于英文水平问题，阅读官方文档时没有找到该问题的解决方法，于是去github提了一个[issue](https://github.com/kartik-v/bootstrap-fileinput/issues/795) ，得到的答案是仔细阅读文档，里面有一个回调函数可以解决问题😍
<!--more-->
### 解决方法
　　关键的配置参数是[uploadExtraData](http://plugins.krajee.com/file-input#option-uploadextradata)
　　具体的代码如下：
```javascript
    //获得额外参数的方法
    fodderType = function() {
            return $("#fodderTypeSelect").val();
        };
　　        
    //初始化fileinput控件（第一次初始化）
    function initFileInput(ctrlName, FileExtensions, fileSize) {
            var control = $('#' + ctrlName);

            control.fileinput({
                language: 'zh', //设置语言
                uploadUrl: "/WxMedia/ImageUpload", //上传的地址
                allowedFileExtensions: FileExtensions, //接收的文件后缀
                showUpload: true, //是否显示上传按钮
                showCaption: true, //是否显示标题,
                maxFileSize: fileSize * 1000, //单位为kb，如果为0表示不限制文件大小
                browseClass: "btn btn-primary", //按钮样式
                previewFileIcon: "<i class='glyphicon glyphicon-king'></i>",
                initialCaption: "请选择上传素材",
                uploadExtraData: function(previewId, index) {   //额外参数的关键点
                    var obj = {};
                    obj.fodder = fodderType();
                    console.log(obj);
                    return obj;
                }
            });
        }
```

### 关键点：
　　可以看到配置文件中`uploadExtraData`的参数是一个函数形式，这是一个回调函数，会在上传时调用，读取配置的额外参数。