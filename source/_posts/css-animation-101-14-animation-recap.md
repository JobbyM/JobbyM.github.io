---
title: '[css-animation-101] 14 animation recap'
date: 2019-03-12 16:51:58
comments: true
tags:
  - 技术
  - css animation 101
  - 翻译
categories: 技术
---

> * 原文地址：[css animation 101 #animation-recap](https://cssanimation.rocks/css-animation-101/#animation-recap)
> * 原文作者：[Donovan Hutchinson](https://cssanimation.rocks/)
> * 译者：[JobbyM](https://github.com/JobbyM)

# 动画回顾
到目前为止，我们已经介绍了很多细节！我希望这是有用的。

当我学习这个的时候，我必须承认我花了一段时间来制作这个动画和关键帧的东西。如果还不清楚，不要灰心。坚持下去，一点一点地，在HTML和CSS中使用动画的各种技巧将变得清晰起来。

在这一章中，我们将花一些时间回顾一下我们本周学到的内容。但首先，我们要看看家庭作业的挑战！

<!--more-->

## 家庭作业挑战：红绿灯
家庭作业的挑战应该很容易。好吧，如果你知道怎么做很容易。我已经创建了一个[基于英国的红绿灯演示](http://codepen.io/donovanh/pen/ogRRdR?editors=010)的更新版本，这次更改了顺序以删除“红色+琥珀色”阶段。

[请看这里的实际情况](http://codepen.io/donovanh/pen/vEqbdw?editors=010)。我已经使配色方案与谷歌建议的美国交通灯的外观相匹配。

## 回顾：动画
本周我们研究了动画`animation` 属性以及它如何与关键帧`keyframes` 一起工作。

### 像transition，又有不同
虽然“动画`animation`” 属性的外观和工作方式类似于“过渡`transition`”，但它有一些细微的差异。过渡`transition` 只有在元素更改时才会发生转换，但动画`animation` 可以立即开始。

使用各种属性，动画可以循环一定次数（或永远循环），甚至可以以负值开始延迟。这将启动动画，使其已在进行中。

默认情况下，动画将从头到尾播放，然后跳转回默认状态。通过使用“向前`forwards`”的“动画方向`animation-direction`”属性，可以使动画在其终点冻结。

动画使用定时功能`timing-functions`，很像过渡`transition`。但是，定时功能适用于每个单独的关键帧，而不是整个关键帧集。相反，你可以在关键帧内指定动画定时功能`animation-timing-function`，以便进行更精细的控制。

最后，可以用速记方式指定动画，就像过渡`transition`一样：
```
animation: keyframe-name 2s forwards linear;
```

### 关键帧
每个动画都需要引用一组关键帧。这些关键帧是一系列百分比，描述动画的每个“阶段stage”。浏览器自动填充间隙。

当你只想从一个状态切换到另一个状态时，关键帧有自己的速记（`to` 和`from`）。

叠加百分比可以让动画在该阶段“暂停`pause`”。

最后，可以省略0%的关键帧，浏览器将采用元素的样式。例如，要使某个元素淡出，我们不必给它一个1的开始不透明度`opacity`（假设元素已经可见）：
```
@keyframes name {
  100% {
    opacity: 0;
  }
}
```

## 把它们放在一起
当我们想要使用动画时，我们总是有两个部分：
```
.element {
  animation: keyframe-name ...
}

@keyframes keyframe-name {
  ...
}
```

## 家庭作业
在这一点上，我们应该清楚动画属性和过渡属性之间的区别。

请看一看[网络动画的一些原理](http://codepen.io/collection/AxKOdY/)示例。每一个都是完全用HTML和CSS制作的，使用关键帧动画。试着用fork 一个，看看你能用它做些什么。
