---
title: '[css-animation-101] 3 transitions'
date: 2019-01-14 10:04:27
comments: true
tags:
  - 技术
  - css animation 101
  - 翻译
categories: 技术
---

> * 原文地址：[css animation 101 #transitions](https://cssanimation.rocks/css-animation-101/#transitions)
> * 原文作者：[Donovan Hutchinson](https://cssanimation.rocks/)
> * 译者：[JobbyM](https://github.com/JobbyM)

# 过渡
让我们看看`transition` 属性。

浏览器过去简单得多。不久前，他们无法渲染图像，只能处理少数的字体。然后CSS 给了我们关于网页外观和感觉的能力。

浏览器中的动画并不新鲜。Flash、Canvas和其他JavaScript 选项为我们提供了动画的方法，但最近CSS 已经成为一个可行的选项。

<!--more-->

## 过渡
CSS允许我们使用`transition` 属性在浏览器中控制动画的一种方式。在浏览器术语中，过渡是从一个状态到另一个状态的动画。
{% asset_img 0.png %}
过渡：A 到B

当我们在元素上使用过渡时，我们告诉浏览器我们希望它插入或改变计算状态之间的变化。
{% asset_img 1.png %}
动画过渡： A 到B([http://codepen.io/donovanh/pen/RNdxqw](http://codepen.io/donovanh/pen/RNdxqw))

例如，我们可以在悬停时应用过渡更改元素的样式，浏览器将在元素的开始样式和新样式之间创建平滑动画。
{% asset_img 2.png %}
动画按钮([http://codepen.io/donovanh/pen/MYQdZd](http://codepen.io/donovanh/pen/MYQdZd))

## 过渡属性
当我们在元素上使用过渡时，有各种属性可以改变过渡的工作方式。我们可以让它变慢或变快，延迟它，甚至使用计时功能来控制变化率。下周我们将深入研究这些，但这有一个很好的例子，说明当组合几个过渡时会发生什么：
{% asset_img 3.gif %}
组合过渡([http://in-ni.com/](http://in-ni.com/))

另一个组合过渡的例子：
{% asset_img 4.gif %}
更多过渡([http://codepen.io/suez/pen/XJGOyL](http://codepen.io/suez/pen/XJGOyL))

## 总结
过渡是从一种状态到另一种状态的变化。例如，悬停在元素上时，其样式可能会更改。过渡允许更改成为平滑动画。

## 家庭作业

你的工作环境怎么样？看看代码，在css 中查找`transition` 属性怎么样？你能看到它在做什么吗？

下次浏览网页时，请在浏览网页时查找过渡示例。查找有趣的更改，例如将新元素添加到页面或将鼠标悬停在按钮上时。一旦你开始寻找，你会发现网络充满了微妙的动画。

下一节：概述动画属性及其与过渡的区别。
