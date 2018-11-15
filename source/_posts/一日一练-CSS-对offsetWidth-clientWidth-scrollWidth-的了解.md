---
title: 一日一练-CSS 对offsetWidth/clientWidth/scrollWidth 的了解
date: 2018-11-15 14:26:17
comments: true
tags:
  - 技术
  - 一日一练
  - CSS
categories: 技术
---

> 子曰：offsetWidth、clientWidth、scrollWidth 区分


## 简介
`offsetWidth` = `border+padding+width+scrollbar`
`clientWidth` = `padding+width`
`scrollWidth` = `padding+width` >= `clientWidth`

<!--more-->

## offsetWidth
参考文档：https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement/offsetWidth

`HTMLElement.offsetWidth` 是一个只读属性，返回一个元素的布局宽度。一个典型的（译者注：各浏览器的`offsetWidth`可能有所不同）`offsetWidth`是测量包含元素的边框(`border`)、水平线上的内边距(`padding`)、竖直方向滚动条(`scrollbar`)（如果存在的话）、以及CSS设置的宽度(`width`)的值。

语法
```
var offsetWidth = element.offsetWidth
```
`offsetWidth` 是一个只读属性。
>这个属性将会四舍五入为一个整数。如果你想要一个小数值，请使用element.getBoundingClientRect()

`offsetWidth` 是一个DHTML 对象模型中的属性。有时候它也可以称为一个元素的物理或图形尺寸，或者`<border-box>`(即CSS3 中的`border-box` 模型)的宽度

## clientWidth
参考文档：https://developer.mozilla.org/zh-CN/docs/Web/API/Element/clientWidth

`Element.clientWidth` 属性表示元素的内部宽度，以像素计。该属性包括内边距，但不包括垂直滚动条（如果有）、边框和外边距

语法
```
var intElemClientWidth = element.clientWidth
```

## scrollWidth
参考文档：https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scrollWidth

`Element.scrollWidth` 是只读属性，表示元素内容的宽度，包括由于滚动而未显示在屏幕中内容

scrollWidth值等于元素在不使用水平滚动条的情况下适合视口中的所有内容所需的最小宽度。 宽度的测量方式与`clientWidth`相同：它包含元素的内边距，但不包括边框，外边距或垂直滚动条（如果存在）。 它还可以包括伪元素的宽度，例如`::before`或`::after`。 如果元素的内容可以适合而不需要水平滚动条，则其`scrollWidth`等于`clientWidth`

语法
```
var xScrollWidth = element.scrollWidth
```
