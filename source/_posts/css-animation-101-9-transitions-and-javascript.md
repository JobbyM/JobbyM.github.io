---
title: '[css-animation-101] 9 transitions and javascript'
date: 2019-01-22 11:21:38
comments: true
tags:
  - 技术
  - css animation 101
  - 翻译
categories: 技术
---

> * 原文地址：[css animation 101 #transitions-and-javascript](https://cssanimation.rocks/css-animation-101/#transitions-and-javascript)
> * 原文作者：[Donovan Hutchinson](https://cssanimation.rocks/)
> * 译者：[JobbyM](https://github.com/JobbyM)

# 过渡和JavaScript
到目前为止，我们一直在使用CSS 中的`transition` 属性在两个状态非悬停和悬停（或焦点）状态之间进行动画制作。

这些过渡需要悬停在元素上。这不是我们触发动画的唯一方法，今天我们将介绍两种方法，使用JavaScript 来实现相同的结果。

<!--more-->

## 添加移除样式类
因为过渡的能力是在两个状态之间进行动画制作，所以我们可以将这些状态创建为单独的类。然后我们使用javascript添加或删除这些类。
{% asset_img 0.gif %}
来源：[http://codepen.io/donovanh/pen/YPbxqa](http://codepen.io/donovanh/pen/YPbxqa)

这个例子由一个按钮和一个内容区组成。最初内容容器有一个`hide`类。在CSS中，`hide`类使其`opacity`为0。

我们在CSS中还有第二个类叫做`show`。此类的`opacity` 为1。

当单击按钮时，我们在`hide` 和`show` 之间切换`div`  的类。为了给它提供动画，我们还对`div` 应用了一个`transition`。

[在这个CodePen 上](http://codepen.io/donovanh/pen/YPbxqa)看到它的实际应用。

如果你想阅读更多，你可能会喜欢这篇文章，[增加你在网上动画的吸引力](http://webdesign.tutsplus.com/tutorials/adding-appeal-to-your-animations-on-the-web--cms-23649)。

在本课程的最后，我们将研究如何在滚动时触发过渡和动画。

## 使用JavaScript 控制过渡
我们可以进一步添加或删除类。使用JavaScript 我们可以直接设置CSS 属性，如下所示：
```
element.style.transition = 'opacity 1s ease-out';
```

在这种情况下，“element” 是我们选择的元素。例如，如果元素具有ID“js-show”，则可以使用`getElementById` 对其应用过渡：
```
document.getElementById('js-show').style.transition = 'opacity 1s ease-out';
```

当我们这样做时，我们必须记住也包括`vendor` 前缀。上面应该这么写：
```
document.getElementById('js-show').style.webkitTransition ='opacity 1s ease-out';
document.getElementById('js-show').style.transition = 'opacity 1s ease-out';
```

这里`webkitTransition` 适用于任何在CSS中使用`-webkit-` 前缀的浏览器。

## 让我们回顾一下
在本章中，我们讨论了`transition` 属性。我们了解到可以使用这个属性告诉浏览器从一个状态到另一个状态的动画。
{% asset_img 0.png %}

在此过程中，我们了解了各种属性：持续时间，延迟和计时功能。

将这些组合在一起我们可以创建有趣的效果组合，甚至可以将多个过渡应用于单个元素。

最后，我们通过介绍如何使用JavaScript 应用这些过渡来完成它。

过渡只是CSS 动画谜题的一部分。接下来我们将介绍动画属性。

## 家庭作业
在我们开始查看动画属性之前，请花些时间考虑如何使用过渡。

你能想到他们可以帮助平滑网页上的互动或状态变化吗？ 他们怎么能增加吸引力呢？
