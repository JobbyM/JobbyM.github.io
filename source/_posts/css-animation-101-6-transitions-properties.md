---
title: '[css-animation-101] 6 transitions properties'
date: 2019-01-16 09:34:40
comments: true
tags:
  - 技术
  - css animation 101
  - 翻译
categories: 技术
---

> * 原文地址：[css animation 101 #transitions-properties](https://cssanimation.rocks/css-animation-101/#transitions-properties)
> * 原文作者：[Donovan Hutchinson](https://cssanimation.rocks/)
> * 译者：[JobbyM](https://github.com/JobbyM)

# 过渡属性
现在我们已经看到了过渡属性的实际应用，让我们看看过渡的属性及其含义。

<!--more-->

## 缩写与普通书写
在编写CSS 时，我们通常可以在缩写属性中将多个属性汇总为一个。例如，`padding` 的缩写可能如下所示：
```
padding: 10px 20px 15px 25px;
```
它等价于：
```
padding-top: 10px;
padding-right: 20px;
padding-bottom: 15px;
padding-left: 25px;
```
同样，我们可以书写过渡的缩写：
```
transition: all 0.5s 1s linear;
```
在这里，缩写对应于：
```
transition: [property] [duration] [delay] [timing-function];
```
每一个属性可以单独写为：
```
transition-property: all;
transition-duration: 0.5s;
transition-delay: 1s;
transition-timing-function: linear;
```
详细看看这些属性。

### transition-property
通常在缩写中第一个说明，这是浏览器将动画制作的属性。例如，要更改背景，可以使用背景。也可以使用`all` 来进行所有适用的CSS 属性过渡。

### transition-duration
`transition-duration` 值告诉浏览器过渡需要多长时间。`transition-duration` 值为3s（3秒）将是`transition-duration` 1000毫秒的三倍。

### transition-delay
`transition-delay` 属性告诉浏览器在应用过渡之前等待的时间。这是一个时间值，可以以秒或毫秒为单位指定。例如，3s 将是3秒，100ms 将是100毫秒。同样，你可以写为0.1s。由你决定。

### transition-timing-function
过渡和动画都使用计时功能。这些有很多，所以与其把它塞进这里，我们明天会讨论更多的定时功能。定时功能真的可以增加动画的生命。

## 某些过渡不起作用
虽然你可以在定位，大小，颜色，边框，背景位置和许多其他位置使用过渡，但有些不能过渡。 `font-family` 无法转换，因为这意味着尝试在两个非常不同的字体图像之间生成帧。

使用CSS 创建的背景图像（例如生成的渐变）不能对其属性进行动画处理。这意味着浏览器会在每个动画帧中重新创建背景图像，因此不受支持。

但是，你可以设置不透明度和背景位置等动画。通过移动背景图像或隐藏它们，来创建有趣的效果。

请参阅此[Baymax 示例](http://cssanimation.rocks/baymax/)，通过移动背景图像来创建动画。
{% asset_img 0.gif %}
来源[http://cssanimation.rocks/baymax/](http://cssanimation.rocks/baymax/)

[此按钮光泽效果](https://cssanimation.rocks/pseudo-elements/)使用类似的效果，其中背景渐变在按钮的前面进行动画处理。
{% asset_img 1.gif %}
来源[https://cssanimation.rocks/pseudo-elements/](https://cssanimation.rocks/pseudo-elements/)

## 家庭作业
我已经创建了[一个基本的Codepen](http://codepen.io/donovanh/pen/NPYNGa?editors=110) 来尝试过渡。

目前，它已设置为从钻石形状过渡到圆形。 尝试更改一些属性，看看会发生什么。

如果你想要更进一步，请按“Fork”按钮创建您自己的版本，然后就可以将工作保存到你自己的Codepen 帐户。
