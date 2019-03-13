---
title: '[css-animation-101] 12 keyframes in action'
date: 2019-03-09 16:26:12
comments: true
tags:
  - 技术
  - css animation 101
  - 翻译
categories: 技术
---

> * 原文地址：[css animation 101 #keyframes-in-action](https://cssanimation.rocks/css-animation-101/#keyframes-in-action)
> * 原文作者：[Donovan Hutchinson](https://cssanimation.rocks/)
> * 译者：[JobbyM](https://github.com/JobbyM)

# 行为中的关键帧
到目前为止，我们已经介绍了动画属性，并有机会了解它如何依赖关键帧。接下来我们将更详细地介绍关键帧。

<!--more-->

## 注意事项
在进入一个实际的例子之前，我想介绍一些关于关键帧的事情。第一种是使用关键字`from` 和`to` 可以看到的备用语法。
```
@keyfames name {
  from {
    ...
  }
  to {
    ...
  }
}
```

虽然这是一种简单的写0% 和100% 的替代方法，但对于简单的动画来说，理解起来更简单，也更有用。

你可能已经注意到，有时在同一行中使用了多个百分比值。这是让动画暂停一段时间或保持特定状态的一种方法。

例如：
```
@keyframes name {
  0%, 20% {
    opacity: 0;
  }
  100% {
    opactiy: 1;
  }
}
```

此示例将使元素从不透明度0 开始，并在动画中保持不可见，直到20%，此时它将开始向不透明度1动画。

明天当我们有多个动画要保持同步时，我们将利用这个功能。

## 例子：保存按钮摆动效果
还记得第一周“保存”按钮的例子吗？让我们重新访问这个例子，看看关键帧是如何与动画属性一起使用来创建效果的。
{% asset_img 0.gif %}
来源[http://codepen.io/donovanh/pen/KwEQdQ](http://codepen.io/donovanh/pen/KwEQdQ)

在添加任何动画之前，我在按钮上添加了一些基本样式，使其看起来像CodePen 的。顶部的橙色边框、深色渐变和白色文本。我在演示中使用绝对定位来确保按钮在屏幕中间。

我通常要做的第一件事是对元素应用动画属性。就像这样：
```
button {
  animation: wiggle 2s linear infinite;
}
```

在本例中，我们将应用一组称为“摆动`wiggle`”的关键帧，动画使用“线性`linear`” 定时功能运行两秒钟。这里还有一个新的属性，无穷大`infinite`。

这里的“无限`infinite`”值用于属性动画迭代计数。我们可以让动画重复一组次数，默认情况下，它们重复一次。在这种情况下，它将重复无数次。

接下来，我们计划出这些关键帧对于“摇摆`wiggle`”动画是什么。结果如下：
```
@keyframes wiggle {
  0%, 7% {
    transform: rotateZ(0);
  }
  15% {
    transform: rotateZ(-15deg);
  }
  20% {
    transform: rotateZ(10deg);
  }
  25% {
    transform: rotateZ(-10deg);
  }
  30% {
    transform: rotateZ(6deg);
  }
  35% {
    transform: rotateZ(-4deg);
  }
  40%, 100% {
    transform: rotateZ(0);
  }
}
```

我们这里有一系列的路径点`waypoints` 供浏览器在它们之间进行动画制作。每个按钮都旋转Z轴上的“保存”按钮。在动画过程中，角度开始变大然后变小。

下面是动画如何随着时间前后倾斜按钮：
{% asset_img 1.gif %}
来源：[http://codepen.io/donovanh/pen/pvXJqp](http://codepen.io/donovanh/pen/pvXJqp)

我们可以看到浏览器在每个关键帧之间的步骤之间创建了中间。这个动画不依赖于复杂的定时功能，而是设法为按钮添加了许多字符。

这是一个[例子](http://codepen.io/donovanh/pen/KwEQdQ)，显示了“保存”按钮在运行中的晃动。

## 家庭作业
我用一个[新的例子](http://codepen.io/donovanh/pen/azgjMz?editors=010)。 它利用关键帧中的“动画定时功能`animation-timing-function`”，并具有一系列关键帧，从而创建相对复杂的动画。

当你拿走一些帧时会发生什么？或者改变百分比？你能让立方体做点别的吗？看看你能不能用这么简单的东西创造一种“生活”的感觉！
