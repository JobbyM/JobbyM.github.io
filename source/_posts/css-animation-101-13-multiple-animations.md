---
title: '[css-animation-101] 13 multiple animations'
date: 2019-03-11 15:13:41
comments: true
tags:
  - 技术
  - css animation 101
  - 翻译
categories: 技术
---

> * 原文地址：[css animation 101 #multiple-animations](https://cssanimation.rocks/css-animation-101/#multiple-animations)
> * 原文作者：[Donovan Hutchinson](https://cssanimation.rocks/)
> * 译者：[JobbyM](https://github.com/JobbyM)

# 多个动画
在本章中，我们将研究如何利用同时运行的多组关键帧。

<!--more-->

## 红绿灯
有时我们希望页面上的多个动画保持同步，但同时每个动画都有自己的时间安排。交通灯就是一个很好的例子。

这里我们有一个简单的（英国式）红绿灯模式：
{% asset_img 0.gif %}
来源[http://codepen.io/donovanh/pen/ogRRdR?editors=010](http://codepen.io/donovanh/pen/ogRRdR?editors=010)

我们有三盏灯，每盏灯都有自己的开关模式。我们可以通过为每个灯光提供自己的动画来创建它。
```
.red {
  animiation: red 10s linear infinite;
}
.amber {
  animiation: amber 10s linear infinite;
}
.green {
  animiation: green 10s linear infinite;
}
```

我们有三个动画，每盏灯一个。每个动画持续相同的时间长度，这样当它们循环时，就不会失去同步。接下来我们需要计划关键帧。

在创建这个示例时，我发现将灯光视为网格很有帮助。动画从左到右进行，每个灯光在特定时间打开或关闭。
{% asset_img 1.png %}

网格分为5列。这意味着我们可以处理20%的“块”，并从这些块创建关键帧集。

每次取一个灯，我们可以从红灯开始。它将在第一个和第二个块中打开，然后在动画的其余部分中关闭。生成的关键帧`keyframes`：
```
@keyframes red {
  0% {
    background: black;
  }
  2%, 40% {
    background-color: red;
  }
  42%, 100% {
    background: black;
  }
}
```

我在开头添加了2%的间隙，让动画的第三部分从42%开始，因为这对交通灯的显示方式增加了一点淡入度。正是这些微妙的东西让一切变得与众不同。

红灯亮了之后，我们看到了网格上的琥珀色灯。

开始时琥珀色灯熄灭，一块亮，然后两块熄灭，最后再次亮。此灯光的关键帧`keyframes`：
```
@keyframes red {
  0%, 20% {
    background: black;
  }
  2%, 40% {
    background: #FF7E00;
  }
  42%, 80% {
    background: black;
  }
  82%, 100% {
    background: #FF7E00;
  }
}
```

最后是绿灯。前两块的灯熄灭，然后两块的灯亮，最后一块的灯熄灭。
```
@keyframes green {
  0%, 40% {
    background: black;
  }
  42%, 80% {
    background: green;
  }
  82%, 100% {
    background: black;
  }
}
```

我们能够[组合到一起](http://codepen.io/donovanh/pen/ogRRdR?editors=010) 查看效果。

## 进一步阅读
要了解有关关键帧`keyframes` 语法主题的更多信息，请阅读[有关该主题的CSS技巧文章](https://css-tricks.com/snippets/css/keyframe-animation-syntax/)。

## 家庭作业
今天的家庭作业是个挑战。红绿灯的例子可能看起来很奇怪，因为它遵循英国的模式。

你能[从红绿灯的例子](http://codepen.io/donovanh/pen/ogRRdR?editors=010)开始，改变它，使其更像美国或其他地方的红绿灯吗？
