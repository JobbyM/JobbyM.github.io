---
title: 一日一练-JS 类数组对象转化成数组
date: 2018-05-08 09:07:18
tags:
  - 技术
  - 一日一练
  - JS
categories: 技术
---

> 子曰：类数组对象

## 类数组对象定义
拥有一个数值length 属性和对应非负整数属性的对象称为类数组对象。
ES5 中的`arguments` 对象，

## 将类数组对象转换为数组
### 方法1
```js
var arr = Array.prototype.slice.call(arguments)
```
`slice()` 没有用其他的额外的参数调用，它的默认参数值会使它具有复制这个`array`（或者类`array`）的效果。

### 方法2
```js
var arr = Array.from(arguments)
```
在ES6 中，可以使用`Array.from(..)` 的内建工具。
