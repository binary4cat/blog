---
title: "Hexo博客迁移到Hugo"
date: 2018-07-26T10:03:53+08:00
tags: ["Hugo"]
categories: ["Blog"]
description: Hexo博客迁移到Hugo
draft: false
---
> 最近将Hexo迁移到了Hugo，过程还是比较曲折的，在这里记录以下迁移的过程。

# Hugo
　　首先还是说一下为什么迁移吧，之前一直在使用Hexo编写博客，Hexo的主题毫无疑问是非常多的，编辑主题也很简单，因为大家可能都会或多或少的掌握前端开发；对比下来hugo的主题挺少，而且要编写的话还要掌握go语言的模板引擎，说实话这个模板引擎确实挺变态的，幸亏现在基本都是前后端分离开发，要不然能被这个模板语法折磨死。
　　迁移的主要原因还是喜欢折腾吧，其次就是正在学习go语言，发现hugo的生成速度真的是快，当初用Hexo的时候，觉得Hexo已经很快了，感受一下hugo才知道这速度真是杠杠滴。
　　hugo还有一个好处就是它本身作为一个程序运行的，没有其他的依赖，很方便的发布到github上，换电脑写博客也很方便，博客原文拉取下来安装个hugo就好。而Hexo就比较麻烦了，依赖太多迁移比较麻烦的。

# 迁移碰到的一些问题
- 时间类型：
    - hexo的时间是不带时区的24小时制时间：`yyyy-MM-dd HH:mm:ss`
    - hugo的时间是带时区的时间格式：`yyyy-MM-ddTHH:mm:ss+HH:mm`
- tags和categories：
    - Hexo的markdown文件头部使用的是yaml格式的，虽然Hugo也是支持这种格式的，但是在提交travis-ci编译的时候报错了：`error calling union: can't iterate over string`，找了一下原因，是把tags和categories的值当作数组遍历了。

# 解决方法
由于上述问题，迁移遇到了困难，一个一个手动改太麻烦了，于是用golang写了一个简单的替换代码，可以直接将hexo的博客文档转换成hugo能够编译的。

// hexo的头部

```yaml
---
title: Hexo博客迁移到Hugo
date: 2018-07-26 10:03:53
categories: Blog
tags: Hugo
description: Hexo博客迁移到Hugo
---
```

// hugo的头部
```yaml
---
title: "Hexo博客迁移到Hugo"
date: 2018-07-26T10:03:53+08:00
tags: ["Hugo"]
categories: ["Blog"]
description: Hexo博客迁移到Hugo
draft: false
---
```

可以看到只需要将tag和categories的值转成数组形式，将date转换成带时区的格式就可以了。

# 结束语
生命在于折腾，如果你也想迁移，恭喜你即将浪费掉陪媳妇儿的时间。
你可以使用以下我写的这个转换工具，在使用前记得备份源文件哦，万一有bug我可不敢负责😂

HxToHu：[github](https://github.com/haijiandong/HxToHu.git)