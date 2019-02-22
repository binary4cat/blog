---
title: "6个Angular开发者必备的VSCode扩展"
date: 2019-02-21T22:02:17+08:00
tags: ["Angular","VSCode"]
categories: ["Frontend"]
description : "6个Angular开发者必备的VSCode扩展"
draft: false
photos: /image/5c4008c23f81db7deb6967541550734744680.png
---

在这篇文章，我将推荐6个我认为是使用VSCode开发Angular必备的扩展，如果你有喜欢的有用插件，也请推荐给我😁。

# 1. 扩展推荐

## 1.1. [TypeScript Hero](https://marketplace.visualstudio.com/items?itemName=rbbit.typescript-hero)

这是一个非常有用的插件，可以帮助你自动管理你的TypeScript `import`，当你复制一段代码是，`TypeScript Hero`会自动感知其中需要`import`的类型，并且自动`import`进来。有效节省你的时间。

其他有用的功能包括排序、删除无用的`import`等。

## 1.2. [Angular Language Service](https://marketplace.visualstudio.com/items?itemName=Angular.ng-template)

这个插件为Angular Template编辑提供了丰富的功能支持。假设你有一个`Component`如下：

```typescript
course = { 
   title: 'The Complete Angular Course', 
   author: { 
      name: 'Mosh Hamedani'
   }
} 
```

借助这个插件，你可以很方便的编辑你的Template:
![Autocompletion.gif](/image/Autocompletion.gif)

## 1.3. [Bracket Pair Colorizer](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer)

这个插件可以使用不同的颜色来标记不同类型的括号：
![Bracket.gif](/image/Bracket.gif)

## 1.4. [Move TS](https://marketplace.visualstudio.com/items?itemName=stringham.move-ts)

随着你的项目的发展，也许你想要将你的`modules`分解的更小、更容易维护。这时你必须移动文件还有文件夹，但是有个问题是移动文件和文件夹后，路径会改变，原先所有`import`它们的地方将失效，这时可能每个文件的`import`需要一个一个手动处理。

使用`Move TS`扩展，可以自动的帮助你管理移动文件和文件夹后的`import`。

## 1.5. [Material Icon Theme](https://marketplace.visualstudio.com/items?itemName=PKief.material-icon-theme)

一个`Material`风格的项目文件资源管理器图标主题。

![Icons.gif](/image/Icons.gif)

## 1.6. [Angular TypeScript Snippets](https://marketplace.visualstudio.com/items?itemName=johnpapa.Angular2)

另一个John Papa开发的有用扩展，可以在开发Angular项目时提高你的工作效率。

这个插件包含了大量的代码段，可以帮助你快速生成代码，免去重复的码字过程。代码段覆盖了`component`、`service`、`directive`、`template`等等组件，它的可以给你提供如下的快捷代码段生成：
![Angular-TypeScript-Snippet.gif](/image/Angular-TypeScript-Snippet.gif)

# 2. 参考资料

[https://morioh.com/p/0e54a8cff696/6-essential-vscode-extensions-for-angular-developers](https://morioh.com/p/0e54a8cff696/6-essential-vscode-extensions-for-angular-developers)