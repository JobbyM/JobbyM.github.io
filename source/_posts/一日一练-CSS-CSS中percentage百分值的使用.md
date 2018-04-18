---
title: 一日一练-CSS CSS中percentage百分值的使用
date: 2018-04-12 19:57:49
comments: true
tags:
  - 技术
  - 一日一练
  - CSS
  - 翻译
categories: 技术
---

> 子曰：学好百分值，考试考百分

首先是确定CSS 中的percentage 都可以应用在CSS 中的哪些属性，以及这些属性的值如何进行计算的，参考[CSS 参考手册](http://css.doyoe.com/)进行统计。

## 定位（Positioning）
1. `top`
  用百分比来定义距离顶部的偏移量。百分比参照包含块的高度。可以为负值。
2. `right`
  用百分比来定义距离右边的偏移量。百分比参照包含块的宽度。可以为负值。
3. `bottom`
  用百分比来定义距离底部的偏移量。百分比参照包含块的高度。可以为负值。
4. `left`
  用百分比来定义距离左边的偏移量。百分比参照包含块的宽度。可以为负值。
<!--more-->
## 尺寸与补白（Dimension）
1. `width`
  用百分比来定义宽度。百分比参照包含块宽度。不允许负值。
2. `min-width`
  用百分比来定义最小宽度。不允许负值。
3. `max-width`
  用百分比来定义最大宽度。不允许负值。
4. `height`
  用百分比来定义高度。百分比参照包含块高度。不允许负值。
5. `min-height`
  用百分比来定义最小高度。不允许负值。
6. `max-height`
  用百分比来定义最大高度。不允许负值
7. `margin`
  用百分比来定义外补白。水平（默认）书写模式下，参照其包含块的`width`进行计算，其它情况参照`height`，可以为负值。
8. `padding`
  用百分比来定义内补白。水平（默认）书写模式下，参照其包含块的`width`进行计算，其它情况参照`height`，不允许负值。

## 背景与边框（Backgrounds and Borders）
1. `border-radius`
  用百分比定义圆形半径或椭圆的半长轴，半短轴。水平方向百分比参照元素宽度（非内容宽度），垂直方向参照元素高度。不允许负值。
2. `border-image-width`
  用百分比指定图像边框的厚度。参照图像边框区域的大小（包含border和padding）进行换算。不允许负值。
3. `background-position`
  用百分比指定背景图像在元素中出现的位置。可以为负值。参考容器尺寸减去背景图尺寸进行换算。
4. `background-size`
  当属性值为百分比时，参照背景图像的`background-origin`区域大小进行换算（而不是包含块大小）。

## 字体（Font）
1. `font-size`
  用百分比指定文字大小。其百分比取值是基于父对象中字体的尺寸。不允许负值。

## 文本（Text）
1. `word-spacing`
  用百分比指定单词间隔。可以为负值。（CSS3）。
2. `text-indent`
  用百分比指定文本的缩进。可以为负值。
3. `vertical-align`
  把当前盒提升（正值）或者降低（负值）这个距离，百分比相对line-height计算。当值为0%时等同于baseline。
4. `line-height`
  用百分比指定行高，其百分比基于文本的font-size进行换算。不允许负值。
5. `text-size-adjust`
  用百分比来指定文本大小在设备尺寸不同的情况下如何调整。

## 伸缩盒（Flexible Box）（新）
1. `flex-basis`
  用百分比来定义宽度。不允许负值。

## 变换（Transform）
1. `transform-origin`
  用百分比指定坐标值。可以为负值。
2. `perspective-origin`
  用百分比指定透视点坐标值，相对于元素宽度。可以为负值。

## 总结
1. 定位中的`top`、`right`、`bottom`、`left` 值为百分比时，都是参考包含块同方向的`width` 或`height` 进行计算值
2. 尺寸与补白中的`height`、`*-height`、`width`、`*-width` 值为百分比时，也是参考包含块同方向的`width` 或`height` 进行计算值
3. 而尺寸与补白中的`padding`、`margin`，则是水平（默认）书写模式下，参照其包含块的`width`进行计算，也就是说同书写的方向相同。（大概是由于`padding` 是同内容相关的，同时没有 **继承性**，在同一书写方向上便于计算值）
4. 字体中的`font-size`则是基于父对象中字体的尺寸（大该是由于`font-size` 有继承性）
5. 文本中的`text-indent` 也是基于包含块的`width` 进行计算的，（推测是同书写方向相同，经验证确实是，当设置`writing-mode` 为`vertical-lr` 时，是基于包含块的`height` 进行计算的）
6. 文本中的`line-height` 基于`font-size` 进行计算，而`vertical-align` 则是基于`line-height` 进行计算的。
7. 背景与边框中的`background-position` 则是基于容器尺寸减去背景图尺寸进行换算（是这么理解的，因为值为50% 时，使得图像居中，也就是）
```
`background-position` = （容器尺寸-背景尺寸）/2
```
8. 背景与边框中的`background-size` 则是基于`background-origin`区域大小（很容易将一个图进行放大或缩小，下面两个图，分别是设置为50% 和33.3333% 的效果）
{% asset_img 50.jpg %}
{% asset_img 33.jpg %}
9. 变换中的`transform-origin` 值为百分比时，横坐标参考元素的宽度，纵坐标参考元素的高度
