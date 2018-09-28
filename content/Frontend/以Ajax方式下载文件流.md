---
title: "以Ajax方式下载文件流"
date: 2018-09-28T17:37:10+08:00
tags: ["JavaScript"]
categories: ["Frontend"]
description : "以Ajax方式下载文件流"
draft: false
---
<!-- TOC -->

- [1. 原有项目的问题](#1-原有项目的问题)
    - [1.1. 使用拼接Form节点的方式](#11-使用拼接form节点的方式)
    - [1.2. 拼接form方式的缺点](#12-拼接form方式的缺点)
- [2. 改造原代码](#2-改造原代码)
    - [2.1. 改成Ajax请求的方式下载文件](#21-改成ajax请求的方式下载文件)
    - [2.2. 问题](#22-问题)
- [3. 参考资料](#3-参考资料)

<!-- /TOC -->
> 最近在修改一个遗留老项目的导出功能，其中有一个功能因为其导出功能没有加载动画，所以在下载耗时稍长的时候，完全不知道页面在干什么，在解决的时候感觉前端框架用的多了，这种原生的问题还是有些棘手的，在此记录一下。

# 1. 原有项目的问题
## 1.1. 使用拼接Form节点的方式
　　由于老项目是使用的ASP.NET MVC编写的，没有使用流行的前端框架，所以在代码中该文件下载功能是用JavaScript拼接了一个form节点的方式，调用DOM的`submit()`方法进行提交请求下载的：
```JavaScript
function downloadFunc(action, params) {
    //创建iframe
    var downloadHelper = $('<iframe style="display:none;" id="downloadHelper"></iframe>').appendTo('body')[0];
    var doc = downloadHelper.contentWindow.document;
    if (doc) {
        doc.open();
        doc.write('');
        doc.writeln(abp.utils.formatString("<html><body><form id='downloadForm_export_file' name='downloadForm' method='post' action='{0}'>", action));
        for (var key in params){
            doc.writeln(abp.utils.formatString("<input type='hidden' name='{0}' value='{1}'>", key, params[key]));
            doc.writeln('<\/form><\/body><\/html>');
            doc.close();
            var form = doc.forms[0];
            if (form) {
                // 提交form表单
                form.submit();
            }
        }
    }
}
```
## 1.2. 拼接form方式的缺点
　　由于要修改的地方是加入下载文件的等待加载动画，所以需要在按钮点击时弹出加载动画，文件返回时停止加载动画；而在拼接form提交的方式中，我们可以在最开始加入一段加载动画(伪代码)`setBusy()`，但是由于form标签的DOM方法`submit()`没有回调函数(可能是我遗漏了)，所以我们无法在文件返回的时候停止加载动画。
　　到目前为止，我们能够想到的方式就是使用Ajax发送请求，在Ajax的回调函数中执行清除加载动画的操作；

# 2. 改造原代码
## 2.1. 改成Ajax请求的方式下载文件
```JavaScript
function downloadFunc(action, params) {
    // 开始加载动画
    setBusy();

    var xhr = new XMLHttpRequest();
    xhr.open('POST', action, true);
    xhr.responseType = 'arraybuffer';
    xhr.onload = function () {
        if (this.status === 200) {
            var filename = "";
            var disposition = xhr.getResponseHeader('Content-Disposition');
            if (disposition && disposition.indexOf('attachment') !== -1) {
                var filenameRegex = /filename[^;=\n]*=((['"]).*?\2|[^;\n]*)/;
                var matches = filenameRegex.exec(disposition);
                if (matches != null && matches[1])
                    // 文件名中含有中文，需要进行解码还原
                    filename = decodeURIComponent(matches[1].replace(/['"]/g, ''));
            }
            var type = xhr.getResponseHeader('Content-Type');

            var blob = typeof File === 'function'
                ? new File([this.response], filename, { type: type })
                : new Blob([this.response], { type: type });
            if (typeof window.navigator.msSaveBlob !== 'undefined') {
                // IE workaround for "HTML7007: One or more blob URLs were revoked by closing the blob for which they were created. These URLs will no longer resolve as the data backing the URL has been freed."
                window.navigator.msSaveBlob(blob, filename);
            } else {
                var URL = window.URL || window.webkitURL;
                var downloadUrl = URL.createObjectURL(blob);

                if (filename) {
                    // use HTML5 a[download] attribute to specify filename
                    var a = document.createElement("a");
                    // safari doesn't support this yet
                    if (typeof a.download === 'undefined') {
                        window.location = downloadUrl;
                    } else {
                        a.href = downloadUrl;
                        a.download = filename;
                        document.body.appendChild(a);
                        a.click();
                    }
                } else {
                    window.location = downloadUrl;
                }

                setTimeout(function () { URL.revokeObjectURL(downloadUrl); }, 100); // cleanup
            }
        }
        // 成功与否清除加载动画
        clearBusy();
    };
    xhr.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');
    xhr.send($.param(params));
}
```

## 2.2. 问题
- 使用Ajax请求返回文件跟普通的请求数据不同，需要将返回的文件流转成Blob对象进行下载；
- 在获取文件名的时候，如果后台没有控制responseHeader中的`Content-disposition`值的编码，那么返回到前端，就会造成乱码；这个问题可以在后端控制，也可以在前端进行解码；

# 3. 参考资料
- [https://my.oschina.net/pingpangkuangmo/blog/376332](https://my.oschina.net/pingpangkuangmo/blog/376332)
- [https://www.haorooms.com/post/js_escape_encodeURIComponent](https://www.haorooms.com/post/js_escape_encodeURIComponent)
- [https://stackoverflow.com/questions/16086162/handle-file-download-from-ajax-post](https://stackoverflow.com/questions/16086162/handle-file-download-from-ajax-post)