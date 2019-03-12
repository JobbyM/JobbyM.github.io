---
title: '[css-animation-101] 11 animation properties'
date: 2019-02-18 11:05:39
comments: true
tags:
  - 技术
  - css animation 101
  - 翻译
categories: 技术
---

> * 原文地址：[css animation 101 #animation-properties](https://cssanimation.rocks/css-animation-101/#animation-properties)
> * 原文作者：[Donovan Hutchinson](https://cssanimation.rocks/)
> * 译者：[JobbyM](https://github.com/JobbyM)

# 动画属性
在我们研究更多动画示例之前，让我们先看看每个动画`animation` 属性。

与过渡`transition` 属性一样，动画`animation` 属性也可以用速记方式编写，或者单独指定其中的任何一个属性。

<!--more-->

## animation-delay
与`transition-delay` 类似，我们可以使用此属性使动画在启动前等待。这在发生多个动画的情况下特别有用。

如果动画循环，则延迟不会在每次循环时应用。延迟仅适用于动画应用于元素时。

实际上可以给这个属性一个负值，如-1s。这将导致动画开始，就好像已经过了一秒钟。

## animation-direction
动画通常从0% 开始，到100% 结束。对于`animation-direction` 我们使用值`normal`、`reverse`、`alternate`和`alternate-reverse` 来控制方向。

“反转`reverse`”使其从100% 播放（和循环）到0%，而“交替`alternate`”则从0% 播放到100%，然后再回到0%。

## animation-duration
这是动画的长度。与`transition-duration` 类似，这需要一个值，例如1秒（`1s`）或200毫秒（`200ms`）。

## animation-fill-mode
默认情况下，将动画播放，然后元素返回其正常状态。使用动画填充模式`animation-fill-mode`，我们可以让动画“Stick”处于结束或开始状态。

使用值`forwards` 可指示动画完成并停留在最后一个关键帧上。而`backwards` 则指示动画结束后，返回到第一个关键帧。

例如[这个例子](http://hop.ie/)。动画播放一次并在最后一帧上完成。这是使用值`forwards`。

## animation-iteration-count
这是动画播放的次数。默认情况下，它将播放一次。你可以指定一个数字，或者“无限`infinite`” 让它永远循环。

## animation-name
`animation-name` 是指与动画关联的关键帧`keyframes`。例如，如果动画名称设置为“foo”，它将使用一组关键帧，如：
```
@keyframes foo {
  ...
}
```

## animation-play-state
如果需要暂停或恢复动画，此属性允许你这样做。它有运行`running` 或暂停`pause` 的值，默认值为运行`running`。一个想法可能是使用JavaScript 在动画上设置这个值。

## animation-timing-function
这在`transition` 中采用了与定时功能属性相同的值，但行为略有不同。虽然定时功能（如“缓出”`ease-out`）适用于整个过渡，但动画的定时功能适用于每个关键帧之间。

这意味着下面的关键帧将看到动画开始的速度很快，并减速到50%，然后快速上升，并在100%之前减速。
```
@keyframes foo {
  0% {
    /* Animation starts fast and ease-out makes it slow down before 50%*/
  }
  50% {
    /* Again, stars fast and slows toward 100% */
  }
  100% {
    /* fin */
  }
}
```

这可能很难处理。通常在创建关键帧动画时，我会选择线性`linear` 定时功能，并使用关键帧`keyframes` 处理动画的步调。

尽管如此，当与动画一起使用时，`cubic-bezier` 定时函数可以产生一些很好的效果，所以请尝试一下

## 在关键帧上使用定时功能
值得注意的是，当为动画指定定时功能时，定时功能将应用于动画的每个关键帧`keyframe`。

这意味着，如果要指定四个关键帧，则计时功能将应用于每个关键帧。当它接近每一个关键帧时，`ease-out`会减慢。

因此，我们通常将动画的计时功能定义为线性，并在每个关键帧的基础上控制步调：
```
@keyframes my-animation {
  0% {
    ...
    animation-timing-function: linear;
  }
  50% {
    ...
    animation-timing-function: ease-out;
  }
}
```

在这种情况下，动画的前半部分将是线性`linear`的，下半部分将使用`ease-out` 定时功能。

## 家庭作业
我在[codepen上](http://codepen.io/donovanh/pen/MYMJRd?editors=010) 创建了一个简单的关键帧动画。属性列在CSS中。尝试更改其中一些属性，看看会发生什么。
