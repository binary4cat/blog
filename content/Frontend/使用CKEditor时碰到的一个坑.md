---
title: 使用CKEditor时碰到的一个坑
date: 2017-02-26T21:54:03+08:00
categories: ["Frontend"]
tags: ["前端组件"]
permalink: use-ckeditor-error
description: 使用CKEditor时碰到的一个坑
photos:
---
# 1. 问题
　　CKEditor的配置文件有很多配置项，我们在使用的时候如果不指定需要那些工具条按钮，默认就会全部显示出来，在配置文件中可以只选择我们需要的一些按钮，比如纯文本编辑的时候，我们会把图片上传按钮去掉，或者不允许斜体加粗之类的操作。
   工具条具体的配置是：
```JavaScript
config.toolbarGroups = [
        { name: 'basicstyles', groups: ['basicstyles', 'cleanup'] },
        { name: 'paragraph', groups: ['list', 'indent', 'blocks', 'align'] },
        { name: 'styles' },
        { name: 'colors' }
    ];
```
<!--more-->
　　当我们要给CKEditor赋默认值的时候，如果默认值中有加粗文本、图片的时候，编辑器是默认过滤掉这些内容的(可能你会问都禁止掉这些按钮了，为什么还要赋值含有这些标记的内容呢？有时候需求就是这么奇葩，相信我！)。
```JavaScript
<!--javascript-->
var str="<h1>balallala</h1><img src="www.hais2.com/image/213.jpg"/>";
<!--html-->
<textarea name="editor" id="editor">${str}</textarea>
```

# 2. 解决方法
　　这时候，我们也可以通过配置来控制默认值内容的过滤(配置是多么强大)。主要看这篇文档：[http://docs.ckeditor.com/#!/guide/dev_advanced_content_filter](http://docs.ckeditor.com/#!/guide/dev_advanced_content_filter)
```JavaScript
//允许所有内容
config.allowedContent = true;
//允许指定的内容
config.allowedContent =
    'h1 h2 h3 p blockquote strong em;' +
    'a[!href];' +
    'img(left,right)[!src,alt,width,height];';
```
# 3. 结束
　　CKEditor的配置非常多，有时候我们并不会看完全部的文档再进行使用，这样在大多时候我们会踩到一些坑，但是没关系，在遇到问题时善于google，一样可以快速的解决问题。
