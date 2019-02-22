---
title: "记一次经常遇到的Vue加载顺序问题"
date: 2018-09-04T22:55:50+08:00
tags: ["Vue"]
categories: ["Frontend"]
description : "记一次经常遇到的Vue加载顺序问题"
draft: true
---

　　对于许多写Vue的小伙伴来说，最开始的时候有点搞不懂Vue的加载顺序，也就是生命周期，虽然官方文档给出了很详细的生命周期解释，但是在实际的开发过程中，我们依然会遇到各种因为大意遗忘而造成的加载顺序BUG，不过也有可能是我自己经常大意吧😅

# 1. 一个典型的问题

　　假如我们在页面中有一个对话框，对话框中列了一个上传控件(我使用的是elementUI上传控件)，该控件是作为新上传图片和编辑已上传图片的功能存在的，所以我们要在对话框弹出的时候，将上传过的图片加载到上传组件中，造成一种修改数据的假象。

*HTML代码* ：

```html
<el-button type='info' icon='el-icon-setting' @click="configEdit">配置</el-button>
...
<!--配置对话框-->
<el-dialog title="配置编辑" :visible.sync="dialogConfigVisible">
    <el-form>
        <el-form-item label="前端图片">
          <el-upload ref="uploadPic" :auto-upload="false" :action="url" name="file">
            <i class="el-icon-plus"></i>
          </el-upload>
        </el-form-item>
    </el-form>
    <div slot="footer" class="dialog-footer">
        <el-button @click="dialogConfigVisible = false">取 消</el-button>
        <el-button type="primary" @click="saveConfig">确 定</el-button>
    </div>
</el-dialog>
```

## 1.1. 第一次尝试

```javascript
/**
* 配置按钮点击操作
*/
configEdit() {
      // 查找已有的图片
      getImg({}).then(res => {
        if (res.data && res.data.code === 1) {
          this.$refs.uploadPic.uploadFiles = [{
            status: "success",
            url: res.data.result
          }]
        }
        this.dialogConfigVisible = true;
      }).catch(err => {
        this.dialogConfigVisible = true;
      })
}
```

　　我们已经上传过一张图片了，现在我们刷新页面，点击配置按钮，弹出的对话框，发现没有任何的图片被加载出来，调试chrome可以看到，请求后台正常返回了图片地址，也加载到了上传组件中。
　　这时候判断是否上传组件还未加载；

## 1.2. 第二次尝试

```javascript
/**
* 配置按钮点击操作
*/
configEdit() {
        this.dialogConfigVisible = true;
      // 查找已有的图片
      getImg({}).then(res => {

        if (res.data && res.data.code === 1) {
          this.$refs.uploadPic.uploadFiles = [{
            status: "success",
            url: res.data.result
          }]
        }
      }).catch(err => {
        this.dialogConfigVisible = true;
      })
}
```
　　
　　这次我们将对话框弹出操作，放在获取图片地址请求的前面，但是依然没有图片加载出来；
　　目前我们排除了对话框展示造成加载未成功的问题，再试试Vue提供的`$nextTick()`函数。

## 1.3. 第三次尝试

```javascript
/**
* 配置按钮点击操作
*/
configEdit() {
      // 查找已有的头图
      getImg({}).then(res => {
        if (res.data && res.data.code === 1) {
          this.$nextTick(() => {
            this.$refs.uploadPic.uploadFiles = [{
              status: "success",
              url: res.data.result
            }]
          })
        }
        this.dialogConfigVisible = true;
      }).catch(err => {
        this.dialogConfigVisible = true;
      })
}
```  

　　这时，再刷新页面点击配置按钮，可以看到已有图片被加载到了上传组件的文件选择框中；  

# 2. 结尾

　　简单总结一下吧，在实际开发中会出现许多和我们想法不同的问题出现，尤其对于对前端不熟悉的后端小伙伴，这时候只能多查文档，多尝试，久而久之对于这些问题就能有一个很准确的处理和判断了。 