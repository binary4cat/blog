title: 5、Hexo博客的写作
date: 2016-03-07T21:12:55+08:00
tags: Hexo博客搭建
categories: Hexo
permalink: Hexo-Getting-Started-6
description: Hexo博客搭建
---
　　这一篇介绍一下Hexo中怎么写博客的，无论怎么折腾，认认真真的写博客才是最主要的。
　　
## 创建文章
　　Hexo创建一篇博文很简单，只需要在博客所在目录下打开“Git Bash Here”命令行窗口，输入：
```
hexo new "你文章的名字"
```
　　然后回车即可创建一篇文章。注意，在博客名称中没有特殊字符时可以不写双引号，但是建议大家每次都写上比较好。
　　创建的博文默认在`\source\_posts\`目录下面，进入该文件夹，找到并打开即可。
　　打开之后可以看到，自动生成了一些信息：<!--more-->
```
title: 5、Hexo博客的写作   #文章标题
date: 2016-03-07 21:12:55   #创建时间
categories: Hexo       #分类
tags: Hexo博客搭建    #标签
```
　　如果有多个标签的话，可以这么写：
```
tags:
- 标签1
- 标签2
- 标签3
...
```

## 写作
　　Hexo创建的文章默认是[Markdown](http://baike.baidu.com/link?url=wjIZLCebx9V8DDHvdOmhnXhSU24T-ICJ6b6RHeHyDW09y8mYs4we611G2VlF2tV9Zd_mXDeczdd41IfoGT66wa)语言格式的，以“.md”为后缀。
　　Markdown是一种文本标记语言，经过简单的书写，可以生成一定格式的网页文本。Markdown的语言非常简单，只要写过几次就会上手了，而且非常棒，我的博客网站中的所有博客都是用Markdown写的，从刚开始的陌生到现在觉得Markdown比富文本编辑器好用多了。
　　编写Markdown文件可以使用文本编辑器打开，比如VS Code、Sublime等都支持Markdown语法。或者可以到网上找找专门的Markdown编辑器，提供了实时效果预览，很方便。

Hexo对Markdown语法的支持不是特别全面，在写作的这段时间里，我总结一些Hexo支持的常用的语法：
**段落开头缩进两格**
　　可以在全角状态下输入两个空格，也就是将输入法切换到全角状态(shift键+空格键)，然后输入两个空格。

**标题字体**
　　标题可以使用“#”号实现，比如下面演示的(注意#号之后的空格)：
```
# 相当于Html中的“h1”标签
## 相当于Html中的“h2”标签
### 相当于Html中的“h3”标签
#### 相当于Html中的“h4”标签
##### 相当于Html中的“h5”标签
```
生成如下内容：
# 相当于Html中的“h1”标签
## 相当于Html中的“h2”标签
### 相当于Html中的“h3”标签
#### 相当于Html中的“h4”标签
##### 相当于Html中的“h5”标签

**粗体和斜体**
　　粗体和斜体分别可以使用两个或一个*号包围住要显示的内容：
```
**这是粗体**
*这是斜体*
```
生成内容如下：
**这是粗体**
*这是斜体*

**短代码**
　　输入一句代码或者一个代码变量，可以使用两个“ ` ”包围要显示的内容即可(在英文状态下，按Tab键上面一行，最左边的按键即可输入该字符)：
```
我要输出一段代码:`public string userName {get; set; }`
```
生成内容如下：
我要输出一段代码:`public string userName {get; set; }`

**代码块**
　　和短代码差不多，不过要三个“ ` ”符号包围代码内容：

```
public class helloWorld
{
    public void say()
    {
        Console.WriteLine("helloWorld");
        Console.ReadKey();
    }
}
```

**引用文字**
　　如果要突出一段文字是引用过来的，可以使用“>”符号：
```
> 这是引用别人的名言：没有理想的时候，可以向钱看。
```
生成内容如下：
> 这是引用别人的名言：没有理想的时候，可以向钱看。

**分割线**
　　分割线可以使用“---”符号实现(三个小横杠)。

**加入图片**
　　加入图片可以加入本地图片和网络图片，本地图片可以在`\source\`文件夹下，新建一个随意名称的文件夹(比如image)，然后把图片放进去；网络图片可以使用图床获得图片的链接；具体使用方法如下：
```
![鼠标指向图片显示的内容](\image\face\捂脸哭.png)
![](http://pic.qqtn.com/file/2013/2014-9/2014091009104543705.jpg)
```
生成内容如下：
![鼠标图片显示的内容](\image\face\捂脸哭.png)
![](http://pic.qqtn.com/file/2013/2014-9/2014091009104543705.jpg)

**插入超链接**
　　插入超链接和插入图片差不多，只是去掉了“!”号：
```
[Is2的博客](http://www.hais2.com)
```
生成如下内容：[Is2的博客](http://www.hais2.com)

**插入音频**
　　可以在音乐网站，比如虾米音乐的web站点去找，每首歌后面都有一个分享的按钮，点击之后就可以获得该歌曲的嵌入代码了，直接粘贴在博客内容中即可，比如：
```
<embed src="http://www.xiami.com/widget/0_1770127756/singlePlayer.swf" type="application/x-shockwave-flash" width="257" height="33" wmode="transparent"></embed>
```
生成如下内容：
<embed src="http://www.xiami.com/widget/0_1770127756/singlePlayer.swf" type="application/x-shockwave-flash" width="257" height="33" wmode="transparent"></embed>

**插入视频**
　　插入视频和插入音乐一样，去视频分享网站，比如youku，可以得到视频的嵌入代码：
```
<iframe height=498 width=510 src="http://player.youku.com/embed/XMTQ4MjI1ODUwOA==" frameborder=0 allowfullscreen></iframe>
```
生成如下内容：
<iframe height=498 width=510 src="http://player.youku.com/embed/XMTQ4MjI1ODUwOA==" frameborder=0 allowfullscreen></iframe>


　　刚开始使用Markdown语法写文章的时候，肯定会有些抵触心理，但是多写写，你就会发现使用起来确实很方便。希望大家能多些博客，分享自己的知识，大家共同进步。