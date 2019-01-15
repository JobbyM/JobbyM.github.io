---
title: '[css-animation-101] 2 creative environments'
date: 2019-01-12 17:09:16
comments: true
tags:
  - 技术
  - css animation 101
  - 翻译
categories: 技术
---

> * 原文地址：[css animation 101 #creative-environments](https://cssanimation.rocks/css-animation-101/#creative-environments)
> * 原文作者：[Donovan Hutchinson](https://cssanimation.rocks/)
> * 译者：[JobbyM](https://github.com/JobbyM)

# 创建环境
> “按照规则你不会学走路。通过实践和摔倒来学习。” Richard Branson

今天我们将看看在浏览器中构建和查看CSS 动画的方法。在开始编码之前，最好有一个工作流程，使其易于入门。

这里将介绍两种方法：在浏览器中开发，以及离线（本地）开发。

<!--more-->

## 在浏览器中
开展小型实验的最简单方法是在线开发。我经常使用的网站是[CodePen](https://cssanimation.rocks/css-animation-101/Codepen.io)。另一个好的是[JS Fiddle](http://jsfiddle.net/)。

对于本课程的其余部分，我将使用[CodePen](http://codepen.io/)作为示例，非常值得熟悉它的工作方式。

CodePen是一个包含编辑模式的编码平台，你可以在其中更改HTML，CSS 和JavaScript 并立即查看结果。屏幕分为四个区域。 预览内容，HTML 区域，CSS 和JavaScript。每个都有一个设置选项，允许你配置语言（例如Sass 而不是CSS）和其他很酷的东西。

## 本地开发
对于涉及更多项目，我更喜欢离线开发。有一些方法可以比在浏览器中工作更高效，更快捷。

### 基本选项：简单的HTML / CSS
最简单的选项是创建一个HTML 文件（filename.html）和一个关联的CSS文件（filename.css），并在HTML 中链接这两个文件。这没关系，但是在浏览器和编辑器之间来回转换很多都会很慢。

我创建了一组HTML 和CSS 文件，你可以复制并使用它们来开始创建。[下载](https://github.com/cssanimation/starter/archive/master.zip)。
{% asset_img 0.jpg %}
开始文件

### Dreamweaver / Macaw / Muse / Coda / Sublime
你可以使用任何喜欢的工具来创建网页。你真正需要的只是一个文本编辑器。其他一些工具带有更多花哨的视觉编辑。

就个人而言，我建议尝试使用代码。理解CSS 的工作方式将有助于解决问题，或使用视觉工具来创建更具表现力的效果。

### Gulp
如果你熟悉Github，Node 和检出代码，可能希望在自己的机器上设置开发环境。

我是Gulp的忠实粉丝。基于NodeJs，它非常快。可以将模块组合在一起将Sass 处理为CSS，为浏览器自动补全和同步浏览器，这样每次更新代码时都无需保持刷新。

如果使用过Grunt 或其他构建工具，那么该过程应该很熟悉。

我创建了一个[Github仓库](https://github.com/cssanimation/gulp-sass-starter)，以加快本地开发速度。如果你对使用Git 感到满意，请继续[阅读readme文件](https://github.com/cssanimation/gulp-sass-starter/blob/master/README.md)获取设置说明。

如果你愿意，可以改进它并进行反馈。团队合作！

## 总结
在学习CSS 动画时，可以尝试不同的方法来创建代码。可以自己托管，或者更喜欢使用CodePen。这些都可以。确保你可以根据需要顺利地从构思到代码。

## 家庭作业
注册[CodePen](http://codepen.io/)。添加一些HTML和CSS，看看结果如何响应变化。在主页上查看一些CodePens 特点也是一个好主意，看看它是如何完成的。

可选：如果要尝试本地开发，请下载本地启动文件：
* 基本选项：[项目启动HTML/CSS 文件](https://github.com/cssanimation/starter/archive/master.zip)
* 高级：[Gulp＆Sass首发](https://github.com/cssanimation/gulp-sass-starter)
下一篇：我们将谈论过渡！
