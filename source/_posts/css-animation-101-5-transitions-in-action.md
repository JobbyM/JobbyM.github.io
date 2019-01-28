---
title: '[css-animation-101] 5 transitions in action'
date: 2019-01-15 20:16:34
comments: true
tags:
  - 技术
  - css animation 101
  - 翻译
categories: 技术
---

> * 原文地址：[css animation 101 #transitions-in-action](https://cssanimation.rocks/css-animation-101/#transitions-in-action)
> * 原文作者：[Donovan Hutchinson](https://cssanimation.rocks/)
> * 译者：[JobbyM](https://github.com/JobbyM)

# 动作过渡
现在我们已经介绍了过渡`transition` 和动画`animation` 属性，现在是时候深入研究过渡并看一些代码了！

<!--more-->

## 过渡
当元素从一个状态更改为另一个状态时，将在浏览器中进行过渡。浏览器自动在每个状态之间绘制帧以创建移动。

过渡`transition` 是CSS 中的属性。就像你给一个元素一个高度，宽度或边框一样，我们也给出了元素过渡。

我们可以在CSS中编写一个过渡：
```
transition: background 0.5s linear;
```

在这种情况下，我们告诉浏览器背景属性的过渡将需要半秒钟，并使用“线性linear”计时功能。

上面的属性可能会导致按钮的背景在悬停时发生变化：
```
button {
  background: white;
  transition: background 0.5s linear;
}

button:hover {
  background: green;
}
```

注意过渡属性为CSS 状态中的一个按钮引用。这告诉浏览器将过渡应用于任何状态更改，例如在悬停时以及从悬停状态更改时。

如果我们仅将过渡属性应用于悬停状态，它只会转换到悬停状态但不会返回。

让我们看看这看起来如何。我已经设置了几个演示。你可能会发现这些示例包含一些不明显的代码。在接下来的几天里，我会详细介绍一下，但是你可以随意修改这些值，看看会发生什么。

## 例子：按钮过渡
{% asset_img 0.gif %}

这是一个展示悬停效果的[CodePen](http://codepen.io/donovanh/pen/MYQdZd)。 在CodePen中，可以更改HTML 和CSS 并立即查看效果。

重要的事是任何属性以`transition-` 开始。我把它们写得很长，以便进行演示，如下所示：
```
transition-property: all;
transition-duration: 0.4s;
transition-timing-function: ease-out;
```

此代码告诉浏览器在非悬停状态和悬停状态之间生成什么类型的移动。它告诉浏览器在0.4秒的持续时间内为所有属性（颜色，大小，位置）设置动画。

尝试更改其中一些值。例如，将“0.4s”更改为更长的内容，例如“2s”（两秒）。动画怎么样？ 你可以将属性从“all”更改为“background”。

要获得有趣的效果，请尝试将`transition-timing-function` 值从`ease-out` 更改为：
`transition-timing-function: cubic-bezier(.59，-0.26，.33,1.42)`
立方贝塞尔计时功能非常有趣。我们将在另一天更详细地介绍时间函数。

## 前缀和浏览器兼容性
当我给出代码示例时，我没有包含供应商前缀。这是为了使代码更易于阅读，但如果你在生产中使用代码，则需要它们。

我喜欢使用Autoprefixer（它是Codepen 上的一个选项，按CSS 部分中的设置“cog”图标），并且可以使用Grunt 或Gulp 等构建工具运行。或者你可以像这样手动写出来：
```
-webkit-transition: ...;
-moz-transition: ...;
transition: ...;
```

## 家庭作业
在今天的示例中编辑按钮并添加你自己的想法。 你可以尝试更改形状，边框或几乎任何属性。玩得开心，目标是确保你熟悉过渡如何影响元素的悬停效果。

对于一些灵感，请查看这个[令人吃惊的悬停风格](http://codepen.io/nxtonic/pen/gbZNKJ)。如果你正在寻找灵感，那里有许多很好的例子。

如果你想要更进一步，请尝试创建一个新的CodePen，其元素在悬停时从一个元素变为另一个元素。看看你是否可以让其中的元素以不同的速率移动。如果你还没有达到这一点，请不要担心，我们将更详细地介绍这些属性。
