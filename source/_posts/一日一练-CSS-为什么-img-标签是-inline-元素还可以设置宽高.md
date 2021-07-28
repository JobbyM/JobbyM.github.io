---
title: 一日一练-CSS 为什么 img 标签是 inline 元素还可以设置宽高
date: 2021-07-28 09:38:11
tags:
  - 技术
  - 一日一练
  - 可替换元素
categories: 技术
---


block 是块级元素， 能设置宽度/高度，margin/padding 水平垂直方向都有效，前后有换行符。
inline：设置宽度/高度无效，margin 在竖直方向无效，padding 在水平垂直方法都有效，前后无换行符。
inline-block：能设置宽度/高度，margin/padding 水平垂直方向都有效，前后有无换行符。


## 为什么 img 标签是 inline 元素还可以设置宽高？
{% asset_img 1.webp %}

这是由于 `img` 属于可替换元素

<!--more-->

## 可替换元素
在 CSS 中，可替换元素（replaced element）的展现效果不是由 CSS 来控制的。这些元素是一种外部对象，它们外观的渲染，是独立于 CSS 的。
  简单来说，它们的内容不受当前文档的样式的影响。CSS 可以影响可替换元素的位置，但不会影响到可替换元素自身的内容。例如 `<iframe>` 元素，可能具有自己的样式表，但它们不会继承父文档的样式。
典型的可替换元素有：

* `<iframe>`
* `<video>`
* `<embed>`
* `<img>`

有些元素仅在特定情况下被作为可替换元素处理，例如：
* `<option>`
* `<audio>`
* `<canvas>`
* `<object>`
* `<applet>`

HTML 规范也说了 `<input>` 元素可替换，因为 "image" 类型的 `<input>` 元素就像 `<img>` 一样被替换。但是其他形式的控制元素，包括其他类型的 `<input>` 元素，被明确地列为非可替换元素（non-replaced elements）。该规范用术语小挂件（`Widgets`）来描述它们默认的限定平台的渲染行为。

用 CSS `content` 属性插入的对象是匿名的可替换元素。它们并不存在于 HTML 标记中，因此是“匿名的”。


