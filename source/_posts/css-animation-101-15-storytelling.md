---
title: '[css-animation-101] 15 storytelling'
date: 2019-03-13 16:55:37
comments: true
tags:
  - 技术
  - css animation 101
  - 翻译
categories: 技术
---

> * 原文地址：[css animation 101 #storytelling](https://cssanimation.rocks/css-animation-101/#storytelling)
> * 原文作者：[Donovan Hutchinson](https://cssanimation.rocks/)
> * 译者：[JobbyM](https://github.com/JobbyM)

# 将故事
既然我们已经讨论了“过渡`transition`”和“动画`animation`”属性，那么让我们将它们结合成具有悬停效果的动画。

<!--more-->

## Heros
许多网站喜欢在其主页的顶部使用一个吸引眼球的大图片。有时被称为“英雄形象”，这通常是一个全幅横幅风格的元素。

我最近发现的一个很好的例子是Fabric 登录页。CSS动画显示了Fabric作为模块化框架的工作方式。
{% asset_img 0.gif %}
来源：[https://get.fabric.io/](https://get.fabric.io/)

另一个有趣的例子是Mailchimp 主页。在这里，英雄形象通过演示如何创建电子邮件来讲述一个故事。
{% asset_img 1.gif %}
来源：[http://mailchimp.com](http://mailchimp.com)

你也可能在[my CSS Animation 101 email course](https://cssanimation.rocks/courses/animation-101/)登录页上看到了它的作用：
{% asset_img 2.gif %}
来源：[https://cssanimation.rocks/courses/animation-101/](https://cssanimation.rocks/courses/animation-101/)

在每一个例子中，他们都使用动画来设置页面的调性，并说明站点是关于什么的。

## 例子：滚动背景
让我们创建一个自己的例子。在这个例子中，我创建了一个“网页”样式的图形，它在屏幕上上上下移动。
{% asset_img 3.gif %}
来源：[http://codepen.io/donovanh/pen/LEwedW?editors=110](http://codepen.io/donovanh/pen/LEwedW?editors=110)

对于一点交互性，当鼠标光标悬停在屏幕上时，动画会暂停并显示一条消息。它同时使用动画和过渡来实现这种效果。

[看这里](http://codepen.io/donovanh/pen/LEwedW?editors=110)

## 第一部分：背景动画
要设置此示例，我们首先从包含它的HTML元素开始：
```
<div class="screen"></div>
```

我们可以使用一些样式使“screen” div 看起来像显示器或iPad：
```
.screen {
  background: #e25865 url(//cssanimation.rocks/screen/images/screen_bg.png) no-repeat top center;
  background-size: 100% auto;
  border: 2em solid #fff;
  border-radius: 1em;
  width: 40em;
  height: 30em;
}
```

我们这里有一些样式定义大小和边框，并[设置背景图像](https://cssanimation.rocks/screen/images/screen_bg.png)。

我们创建的效果基于移动背景图像。背景图像比屏幕高，`background-size`为100% 自动。这意味着背景将适合我们容器的宽度，但更高。

通过设置背景图像动画，我们现在可以编写关键帧`keyframes`，使其看起来像是有人在滚动网页：
```
@keyframes scroll {
  0%, 10% {
    background-position: 0 0;
  }
  20%, 30% {
    background-position: 0 -22em;
  }
  50%, 60% {
    background-position: 0 -44em;
  }
  90%, 100% {
    background-position: 0 0;
  }
}
```

我们设置动画的属性是背景位置`background-size`。有了这个属性，我们可以上下移动它。它从0开始，这意味着到左边和到顶端的距离都为零。

在下一帧中，我们让背景向上移动22个ems，然后向上移动44个ems，然后返回页面顶部。让我们创建一个动画属性，将其应用于“screen”元素。
```
.screen {
  animation: scroll 5s infinite cubic-bezier(.52,-0.39,.3,1.43);
}
```
这个CSS应用了一组称为“滚动`scroll`”的关键帧，告诉它需要5秒钟，永远运行，并使用一个`cubic-bezier` 定时函数。在这种情况下，cubic bezier 函数给动画的弹性，因为没有它的运动看起来不那么逼真。

我在[cubic-bezier.com](http://cubic-bezier.com/#.52,-0.39,.3,1.43)上生成了这个Bezier。如果你还没有给那个网站加上书签，我绝对会推荐你去做！

## 第二部分：添加悬停过渡
当动画完成或者你想让人们专注于其他事情时，暂停或者停止动画是个好主意。持续动画可能会分散注意力，所以让我们利用动画播放状态`animation-play-state`在悬停时暂停动画。
```
.screen:hover {
  animation-play-state: paused;
}
```

这意味着当光标悬停在动画上时，它将暂停。当光标再次移动时，将恢复其默认播放状态。

你也可以用JavaScript 来实现这一点。一种可能是让一些javascript在用户与页面的另一部分交互时禁用动画，或者在它们滚动时禁用动画。稍后我们将了解如何启用滚动动画。

### 添加消息
当用户悬停在元素上时，我们还可以进一步进行消息过渡`transition`。要做到这一点，我们需要更多的HTML：
```
<div class="screen">
  <div class="message">Hover message!</div>
</div>
```

在[代码笔CSS](http://codepen.io/donovanh/pen/LEwedW?editors=110)中，我们将此消息放置在“屏幕”的中间，使其不可见。
```
.message {
  /*... positioning styles ...*/
  opacity: 0;
  transition: all 0.4s ease-out;
}
```

然后我们可以使用一个过渡`transition` 在悬停时显示它：
```
.screen:hover .message {
  opacity: 1;
}
```

因为我们在“消息”样式上设置了一个过渡`transition`，所以当光标悬停在元素上和离开元素时，它都会创建动画。暂停的动画和过渡效果如下：
{% asset_img 4.gif %}
来源：[http://codepen.io/donovanh/pen/LEwedW?editors=110](http://codepen.io/donovanh/pen/LEwedW?editors=110)

## 总结
在本章中，我们将动画和过渡结合在一起，以创建一种效果，这对于登录页面和产品浏览很有用。我们利用动画播放状态`animation-play-state` 来确保动画在需要时停止。

## 家庭作业
花点时间想想我们到目前为止所涵盖的内容。

我们覆盖了很多。结合动画和过渡是一个强大的方式，使网页生动。

当考虑如何将其应用于你的工作时，请考虑如何对其进行控制。动画什么时候可以为你的用户带来好处，什么时候起反作用？知道如何制作动画是很好的，但更应清楚什么时候不制作。
