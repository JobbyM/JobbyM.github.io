---
title: 一日一练-CSS line-height 文字垂直居中的秘密
date: 2018-07-12 17:39:13
tags:
  - 技术
  - 一日一练
  - CSS
categories: 技术
---


> 子曰：line-height 垂直居中

`line-height`

将`line-height` 的计算值减去`font-size` 的计算值。这个值是总的行间距。需要注意，这可能是一个负值。然后行间距再除2，将行间距的一半分别应用到内容区的顶部和底部。

举个例子，假设`font-size` 为14 像素高，而且`line-height` 计算为18 像素。其差（4像素）除以2，将其一半分别应用到内容区的顶部和底部。
