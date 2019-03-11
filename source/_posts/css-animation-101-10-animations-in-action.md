---
title: '[css-animation-101] 10 animations in action'
date: 2019-01-29 18:30:20
comments: true
tags:
  - 技术
  - css animation 101
  - 翻译
categories: 技术
---

> * 原文地址：[css animation 101 #animations-in-action](https://cssanimation.rocks/css-animation-101/#animations-in-action)
> * 原文作者：[Donovan Hutchinson](https://cssanimation.rocks/)
> * 译者：[JobbyM](https://github.com/JobbyM)


# 行为中的动画
现在我们已经研究了过渡属性，让我们更深入地研究一下动画属性。
{% asset_img 0.png %}

<!--more-->

## 共生关系
动画`animation` 属性应用于元素，就像过渡`transition` 一样。它还需要第二部分，称为关键帧`keyframes`。
```
.element {
  animation: ...
}

@keyframes animation-name {
  /* Keyframes go here */
}
```

单独定义关键帧`keyframes` 的一个好处是，它允许我们创建可以重复使用多次的动画。

## 动画属性
将这些关键帧应用到元素是通过“动画`animation`”属性完成的。它非常类似于过渡`transition`，但有一些额外的属性。动画可以写成以下速记：
```
animation: change-background 4s linear infinite;
```

作为单个属性书写，它看起来像：
```
animation-name: change-background;
animation-duration: 4s;
animation-timing-function: linear;
animation-repeat: infinite;
```

如果一个过渡`transition` 具有一个属性`property`，例如“background”或“all”，则将为动画`animation` 属性指定描述动画序列的一组关键帧的名称。

动画有一些过渡不具备的属性。例如，我们可以告诉动画来回交替，而不是每次从一开始就循环。

## 关键帧
CSS中的一组关键帧是一个动画过程中的一系列停止点。每个“关键帧`keyframe`”都是一个百分比。

用一个例子来描述很有帮助。让我们从网页上的一个DIV开始，它会随着时间的推移而改变背景。它从蓝色背景开始，变为橙色背景，最后变为绿色。
{% asset_img 1.gif %}

如果我们试图向某人解释这些背景色是如何随时间变化的，我们可能会说：
“从蓝色背景开始，再到中间的橙色背景，最后是绿色背景。”

或者，如果我们想更精确一些，我们可以用百分比来解释时间：
“从0%的蓝色背景开始，然后50%的橙色背景，100%的绿色背景”

然后我们可以总结如下：
```
0% Blue
50% Green
100% Orange
```

利用这些百分比，我们创建了一系列动画可能通过的“路径点`waypoints`”。我们现在需要做的就是告诉浏览器，这些百分比实际上是关键帧`keyframes`，并给动画命名。结果是：
```
@keyframes change-background {
  0% {
    background: blue;
  }
  50% {
    background: orange;
  }
  100% {
    background: green;
  }
}
```

这个动画被称为“改变背景`change-background`”。稍后我们将在将关键帧keyframes 应用到元素时使用它。

当从上到下阅读代码时，百分比描述了动画中每个关键帧发生的距离。我们可以在这里看到它的作用：
{% asset_img 2.gif %}

在本例中，我已将“动画方向`animation-direction`” 属性更改为“交替`alternate`”。在[CodePen](http://codepen.io/donovanh/pen/NPZqej) 上查看

## 前缀
目前仍然需要在动画属性上使用`-webkit-` 前缀。我不会将它添加到示例中，如果需要在Safari 等浏览器中工作，则仍需要前缀。

在Codepen 中，可以在css设置中使用“autoprefixer”选项。对于本地开发，我使用的是Gulp 版本的Autoprefixer。[Prefix Free](http://leaverou.github.io/prefixfree/) 也是一个不错的选择。

## 家庭作业
打开这个[关键帧示例](http://codepen.io/donovanh/pen/WbqNwd?editors=110) 并尝试更改代码。看看你能不能把它弄坏，修好。更好的是，如果你想出一些酷的东西，告诉我！

我喜欢看你的情况。你可以发邮件`donovan@cssanimation.rocks` 给我或者在[Twitter](https://twitter.com/donovanh)上联系我。
