---
title: '[css-animation-101] 8 multiple transitions'
date: 2019-01-22 10:12:28
comments: true
tags:
  - 技术
  - css animation 101
  - 翻译
categories: 技术
---

> * 原文地址：[css animation 101 #multiple-transitions](https://cssanimation.rocks/css-animation-101/#multiple-transitions)
> * 原文作者：[Donovan Hutchinson](https://cssanimation.rocks/)
> * 译者：[JobbyM](https://github.com/JobbyM)

# 多个过渡
到目前为止，我们已经讨论了一个过渡如何在一个状态和另一个状态之间创建移动。接下来，我们将看到当我们对具有多个更改的元素应用单个过渡时会发生什么，以及如何同时使用多个过渡来微妙地改进动画。

<!--more-->

## 例1：Fancy button
虽然我们已经看到了一个简单的按钮悬停效果，但我们可以将多个过渡组合合成在一个按钮上，以获得更有趣的效果。
{% asset_img 0.gif %}
来源:[http://codepen.io/donovanh/pen/YPMGpJ](http://codepen.io/donovanh/pen/YPMGpJ)

在[本例](http://codepen.io/donovanh/pen/YPMGpJ)中，悬停效果结合了几种状态更改，但都是由单个过渡定义的：
```
transition: transform 0.4s cubic-bezier(.72,-0.61,.25,1.51);
```

这就是它的工作原理。按钮由两个图标和两段文本组成。初始（非悬停）状态是“Follow me”文本和Twitter 图标位于按钮内。我将`@` 符号和“cssanimation”文本放在按钮外，如下所示：
{% asset_img 1.png %}

然后添加了一个悬停状态，其中按钮外部的元素位于按钮内部，如：
{% asset_img 2.png %}

我这样做是使用CSS 过渡。 例如，Twitter 符号使用绝对定位来定位。在设置时，我将它定位在我想要使用`left` 和`top` 值的位置：
```
.icon {
  position: absolute;
  left: 0.75em;
  top: 0.75em;
}
```

然后为按钮添加悬停状态，并使用转换将Twitter 图标放在按钮外：
```
a:hover .icon {
  transform: translateY(3em);
}
```

添加`overflow: hidden` 到容器中意味着按钮之外的元素不会显示。

如果没有过渡，图标将突然消失。由于按钮内的每个元素都是一个span，因此我可以同时对它们应用转换：
```
span {
  transition: transform 0.4s cubic-bezier(.72,-0.61,.25,1.51);
}
```

这意味着，如果任何span 元素的状态发生变化（如悬停），它们都将被过渡。同样的技巧也适用于按钮的其他部分。

你可以[在CodePen上完整地看到这个例子](http://codepen.io/donovanh/pen/YPMGpJ)。

## 例2：Background reveal
在这个例子中，我设置了一个包含一些文本的卡片，并在悬停时显示文本。
{% asset_img 3.gif %}
来源：[http://codepen.io/donovanh/pen/LEvjJg](http://codepen.io/donovanh/pen/LEvjJg)

卡的初始（非悬停）状态显示标题，但段落文本不透明度为零。悬停时，我们将其更改为1以显示文本，并更改文本容器的高度。

如果没有过渡，看起来[就像这样](http://codepen.io/donovanh/pen/PwgKLw?editors=110)。当我们将鼠标悬停在卡片上时，变化是突然的。

加上两个过渡，完全改变气氛。[结果就是这样](http://codepen.io/donovanh/pen/LEvjJg)。

第一个过渡（这次是速记）如下所示：
```
transition: all 0.5s cubic-bezier(.48,-0.28,.41,1.4);
```

这会告诉浏览器在0.5 秒的时间内对所有属性设置动画，并使用`cubic-bezier` 过渡为其提供一些反弹。在这种情况下，它会影响文本容器的高度。

第二个过渡使文本移动。这里使用了一个ease-out 定时功能：
```
transition: all 0.4s ease-out;
```

我们可以通过改变悬停状态来达到很多目的。在这个例子中，info div的height和paragraph都在`.card:hover` 状态下给出了新的值。

在本例中，我们使用两个过渡，以便每个移动部件以不同的方式移动。让元素以不同的速度移动确实有助于增加过渡的吸引力。

也可以在[CodePen 上看到这个例子](http://codepen.io/donovanh/pen/LEvjJg)。

## 单个元素上的多个过渡
除了在多个元素上使用多个过渡之外，我们还可以在单个元素上使用多个转换。

这种情况下，你需要一个元素的背景与它的边框分开更改。对所有属性应用单一过渡可能对两者都太粗糙了。

我们可以通过将多个过渡组合成一个声明来实现这一点。多个过渡用逗号分隔。
例如：
```
transition: background 1s ease-out, border 0.5s linear;
```

这里的第一个过渡只在背景上工作，第二个（在逗号之后）只应用于边框。这意味着更改背景的悬停状态需要1秒钟，而边界的转换需要0.5秒。

## 家庭作业
在本章中，我们研究了如何通过单个过渡处理多个效果，以及如何将多个过渡一起使用。值得一看每个CodePen 上的示例：
* 示例1：[Fancy button](http://codepen.io/donovanh/pen/YPMGpJ)
* 示例2：[Cat Hover card](http://codepen.io/donovanh/pen/LEvjJg)

你能想到这些类型的过渡对你当前正在处理的项目有帮助吗？

到目前为止，我们已经报道了很多。接下来，我们将了解如何使用JavaScript 应用这些转换。
