---
title: 一日一练-CSS BFC、IFC、GFC和FFC
date: 2018-10-16 18:12:40
comments: true
tags:
  - 技术
  - 一日一练
  - CSS
categories: 技术
---

> 子曰：一日一练-CSS BFC、IFC、GFC和FFC

## 0x00
FC的全称是：`Formatting Contexts`，是W3C CSS2.1规范中的一个概念。它是页面中的一块渲染区域，并且有一套渲染规则，它决定了其子元素将如何定位，以及和其他元素的关系和相互作用。

## 0x01
BFC(`Block Formatting Contexts`)直译为"块级格式化上下文"。`Block Formatting Contexts` 就是页面上的一个隔离的渲染区域，容器里面的子元素不会在布局上影响到外面的元素，反之也是如此。

## 0x02
IFC(`Inline Formatting Contexts`)直译为"内联格式化上下文"，`IFC`的`line box`（线框）高度由其包含行内元素中最高的实际高度计算而来（不受到竖直方向的`padding/margin`影响)
IFC中的`line box`一般左右都贴紧整个IFC，但是会因为`float`元素而扰乱。`float`元素会位于IFC与与`line box`之间，使得`line box`宽度缩短。 同个ifc下的多个`line box`高度会不同。 `IFC`中时不可能有块级元素的，当插入块级元素时（如`p`中插入`div`）会产生两个匿名块与`div`分隔开，即产生两个`IFC`，每个`IFC`对外表现为块级元素，与`div`垂直排列。

## 0x03
GFC(`GridLayout Formatting Contexts`)直译为"网格布局格式化上下文"，当为一个元素设置`display`值为`grid`的时候，此元素将会获得一个独立的渲染区域，我们可以通过在网格容器（`grid container`）上定义网格定义行（`grid definition rows`）和网格定义列（`grid definition columns`）属性各在网格项目（`grid item`）上定义网格行（`grid row`）和网格列（`grid columns`）为每一个网格项目（`grid item`）定义位置和空间。

## 0x04
FFC(`Flex Formatting Contexts`)直译为"自适应格式化上下文"，`display`值为`flex`或者`inline-flex`的元素将会生成自适应容器（`flex container`），可惜这个牛逼的属性只有谷歌和火狐支持，不过在移动端也足够了，至少safari和chrome还是OK的，毕竟这俩在移动端才是王道
