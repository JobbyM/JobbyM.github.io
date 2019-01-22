---
title: '[css-animation-101] 4 animations'
date: 2019-01-14 20:41:17
comments: true
tags:
  - 技术
  - css animation 101
  - 翻译
categories: 技术
---

> * 原文地址：[css animation 101 #animations](https://cssanimation.rocks/css-animation-101/#animations)
> * 原文作者：[Donovan Hutchinson](https://cssanimation.rocks/)
> * 译者：[JobbyM](https://github.com/JobbyM)

# 动画
到目前为止，我们已经讨论了为什么要进行动画制作，找到了一些灵感来源，查看了可能对开发有用的工具和站点，并了解了什么是过渡。

接下来，让我介绍动画属性。

<!--more-->

## 浏览器中的动画
过渡和动画是相似的。两者都采用CSS 属性的形式，并具有持续时间、延迟和其他控制浏览器如何创建移动的方法。

虽然过渡都是关于平滑从状态A 到状态B 的变化，但动画是描述多个步骤的一种方法。
{% asset_img 0.gif %}
过渡：A 到B

{% asset_img 1.png %}
动画：A 到B 到C

动画对于浏览器中更复杂的移动非常有用。在上面的例子中，有3种状态（A，B和C）。过渡只会从A 到C，而动画允许我们指定B 步骤的样子，并确保动画遵循所有的三个步骤。

动画也表现得有点不同。它们可以自动开始。但是过渡可能需要添加类或更改状态（如悬停），但动画可以在页面加载时启动。

这意味着如果讲故事或引起对页面上某些事物的注意，动画可能是一个不错的选择。

## 例子
我们在Codepen 上看到的“保存”按钮移动是一个很好的练习动画示例。
{% asset_img 2.gif %}
Codepen 上的保存按钮([http://codepen.io/donovanh/pen/KwEQdQ](http://codepen.io/donovanh/pen/KwEQdQ))

它能帮助人们注意到按钮。

该效果由一系列关键帧(`keyframes`)组成，这些关键帧告诉浏览器从左到右晃动按钮。我们将在第3周更深入地研究关键帧。
另一个例子是[Fabric](https://get.fabric.io/)的登陆页面，这是一个来自Twitter 的应用程序构建平台。CSS 动画在这里被很好地用于帮助沟通结构的模块化方面，同时建立可信度：
{% asset_img 3.gif %}
Fabric hero 图片动画([https://get.fabric.io/](https://get.fabric.io/))

最后，我去年制作的一个会议预告网站的例子。这是一个CSS Mac Plus，用于在[theWeb.is预告网站](http://theweb.is/)上展示欢迎信息。
{% asset_img 4.gif %}
使用CSS 制作的Mac Plus

这个CSS Mac Plus可以在[CodePen](http://codepen.io/donovanh/full/HGqjp/)上获得，这是构建它的[分步指南](https://cssanimation.rocks/macplus/)。

## 过渡与动画
过渡是指浏览器从一个状态动画到另一个状态（A 到B）。它们通常由悬停在元素上的动作触发，或者使用JavaScript 添加或删除类。

动画更加复杂，你可以根据需要使用尽可能多的关键帧创建动画序列。它们会自动触发，并且可以循环。
{% asset_img 5.gif %}
过渡与动画([https://cssanimation.rocks/transition-vs-animation/](https://cssanimation.rocks/transition-vs-animation/))

我们会花些时间研究动画属性。在此期间，请注意在线动画的有趣用法，如果找到一个好动画，[请告诉我！](donovan@cssanimation.rocks)

## 家庭作业
你能想到在你的网页上使用动画的方式吗？浏览时要留意动画。注意某些事物何时以一种引起人们注意的方式移动。在这些情况下，它通常是动画。

如果你已下载入门HTML 和CSS，请查看动画属性。与过渡不同，动画需要第二部分，称为关键帧。尝试更改一些值，看看会发生什么。
