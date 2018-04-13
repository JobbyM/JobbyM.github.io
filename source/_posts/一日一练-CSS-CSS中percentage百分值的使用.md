---
title: 一日一练-CSS CSS中percentage百分值的使用
date: 2018-04-12 19:57:49
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
1. top
  用百分比来定义距离顶部的偏移量。百分比参照包含块的高度。可以为负值。
2. right
  用百分比来定义距离右边的偏移量。百分比参照包含块的宽度。可以为负值。
3. bottom
  用百分比来定义距离底部的偏移量。百分比参照包含块的高度。可以为负值。
4. left
  用百分比来定义距离左边的偏移量。百分比参照包含块的宽度。可以为负值。

## 尺寸与补白（Dimension）
1. width
  用百分比来定义宽度。百分比参照包含块宽度。不允许负值。
2. min-width
  用百分比来定义最小宽度。不允许负值。
3. max-width
  用百分比来定义最大宽度。不允许负值。
4. height
  用百分比来定义高度。不允许负值。
5. min-height
  用百分比来定义最小高度。不允许负值。
6. max-height
  用百分比来定义最大高度。不允许负值
7. margin
  用百分比来定义外补白。水平（默认）书写模式下，参照其包含块的width进行计算，其它情况参照height，可以为负值。
8. padding
  用百分比来定义内补白。水平（默认）书写模式下，参照其包含块的width进行计算，其它情况参照height，不允许负值。

## border-radius
1. border-radius
  用百分比定义圆形半径或椭圆的半长轴，半短轴。水平方向百分比参照元素宽度（非内容宽度），垂直方向参照元素高度。不允许负值。
2. border-image-width
  用百分比指定图像边框的厚度。参照图像边框区域的大小（包含border和padding）进行换算。不允许负值。
3. background-position
  用百分比指定背景图像在元素中出现的位置。可以为负值。参考容器尺寸减去背景图尺寸进行换算。
4. background-size
  当属性值为百分比时，参照背景图像的background-origin区域大小进行换算（而不是包含块大小）。

## 字体（Font）
1. font-size
  用百分比指定文字大小。其百分比取值是基于父对象中字体的尺寸。不允许负值。

## 文本（Text）
1. word-space
  用百分比指定单词间隔。可以为负值。（CSS3）。
2. text-indent
  用百分比指定文本的缩进。可以为负值。
3. vertical-align
  把当前盒提升（正值）或者降低（负值）这个距离，百分比相对line-height计算。当值为0%时等同于baseline。
4. line-height
  用百分比指定行高，其百分比基于文本的font-size进行换算。不允许负值。
5. text-size-adjust
  用百分比来指定文本大小在设备尺寸不同的情况下如何调整。

## 伸缩盒（Flexible Box）（新）
1. flex-basis
  用百分比来定义宽度。不允许负值。

## 变换（Transform）
1. transform-origin
  用百分比指定坐标值。可以为负值。
2. perspective-origin
  用百分比指定透视点坐标值，相对于元素宽度。可以为负值。
