---
title: '[css-animation-101] 7 timing functions'
date: 2019-01-21 18:04:50
comments: true
tags:
  - 技术
  - css animation 101
  - 翻译
categories: 技术
---

> * 原文地址：[css animation 101 #timing-functions](https://cssanimation.rocks/css-animation-101/#timing-functions)
> * 原文作者：[Donovan Hutchinson](https://cssanimation.rocks/)
> * 译者：[JobbyM](https://github.com/JobbyM)

# Timing functions
定时功能是对转换速度变化率的描述。当动画以固定的线性速度出现时，它们看起来毫无生气。使用定时功能可以使转换更逼真。

例如，在第一幅图像中，转换使用`linear` 定时功能：
{% asset_img 0.gif %}
Linear 过渡

<!--more-->

对于这个例子，我们使用定制的`cubic-bezier` 定时功能：
{% asset_img 1.gif %}
Cubic-bezier 定时功能

在本例中，`cubic-bezier` 方法告诉动画在快速移动到第二个状态之前稍微向后摇摆，并且实际上到达正确位置之前稍微超过它。

每个示例的开始和悬停状态的CSS 是相同的，所更改的只是计时函数。

让我们逐一研究一下它们是如何影响我们的元素的移动方式的。

如果你想在一个例子中使用这些，我设置了一个[CodePen 例子](http://codepen.io/donovanh/pen/GgaRNv)。

## Linear
{% asset_img 2.gif %}
Linear

线性过渡从开始到结束以稳定的速率移动。因为在过渡过程中没有曲线，所以它从不加速或减速。如果制作需要稳定移动的动画，例如场景经过火车窗口背景或稳定旋转的月亮，这可能很有用。

## Ease-in
{% asset_img 3.gif %}
Ease-in

`ease-in` 定时功能开始缓慢，并在过渡结束时加速。它类似于一个球开始从山上滚下来，在底部以最快的速度结束。或者一条无聊的鱼左右游动。

## Ease-out
{% asset_img 4.gif %}
Ease-out

`ease-out` 与`ease-in` 相反。它开始得很快，在过渡期结束时会变慢。当有什么东西需要出现时，就像是从屏幕外冲入，然后慢慢停下来一样，这很有用。

## Ease-in-out
{% asset_img 5.gif %}
Ease-in-out

`ease-in-out` 兼容了`easy-in` 和`ease-out` 功能。它开始缓慢，在过渡的中间部分加速，然后减速到最后。它可以说明汽车从静止开始，加速，然后在停止前放慢速度。如果制作加载动画，这样的东西看起来会很不错。

## Cubic-bezier
{% asset_img 6.gif %}
Cubic-bezier(自定义)

到目前为止我们看到的所有定时功能都是三次贝塞尔曲线（cubic bezier curve）的例子。这是描述定时功能的“形状”的曲线。

通过这种方式，指定`cubic-bezier` 定时功能就像创建我们自己的定时功能一样。

它们由4个值组成，代表两个坐标。三次贝塞尔曲线可以如下所示：
```
transition-timing-function: cubic-bezier(1,-0.49,.13,1.09);
```

这里的两个坐标是(1, -0.49) 和(.13, 1.09)。在图表上，它们看起来像这样：
{% asset_img 7.png %}
来源[http://cubic-bezier.com/#1,-0.49,.13,1.09](http://cubic-bezier.com/#1,-0.49,.13,1.09)

我不是手工创建这些，而是使用[cubic-bezier.com](http://cubic-bezier.com/)。这是创造一些有趣效果的好方法。

当使用大于1的值时，它们真的很有趣。可以创建过冲和反弹的过渡。

## Steps
{% asset_img 8.gif %}
Steps

在大多数定时功能涉及曲线的情况下，step 功能将转换分为一组步骤并在每个步骤之间跳转。例如，如果指定`step(4)`，则过渡将持续时间分为4个离散跳转（如上图所示）。
{% asset_img 9.png %}

这对sprite 动画很有用。例如，加载loading spinner 或动画视频游戏角色。通过设置一系列帧开始时的背景位置，可以使用步骤定时功能跳过每个帧并创建移动的外观。

要看到一个很好的例子，请查看[twitter的fave按钮](https://cssanimation.rocks/twitter-fave/)动画。

你还可以指定过渡是为持续时间片段保留第一帧，还是保留最后一帧。默认设置为“end”，因为这假定在动画开始之前已经显示了精灵中的第一帧。

我们可以指定在设置步骤时适用的步骤：
```
transition: all 2s steps(10, start);
transition: all 2s steps(10, end);
```

## 更多例子
如果想阅读更多内容并查看其他示例，我已经在这里介绍了[定时功能](https://medium.com/css-tutorials/bouncy-transitions-c0c8085d489)的主题。

## 家庭作业
从[上一个家庭作业示例](http://codepen.io/donovanh/pen/NPYNGa?editors=110)开始，尝试更改过渡定时功能值，看看它如何更改过渡的感觉。

你也可以尝试[在此演示](http://codepen.io/donovanh/pen/GgaRNv)中更改值。从技术上讲，这是一个动画而不是一个过渡，但定时功能的应用方式相同。
