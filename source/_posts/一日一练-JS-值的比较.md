---
title: 一日一练-JS 值的比较
date: 2018-05-09 18:21:53
tags:
  - 技术
  - 一日一练
  - JS
categories: 技术
---

> 子曰：值的比较

由Alex Dorey ([@dorey on GitHub](https://github.com/dorey))提供的表格，此图包含`!=`、`==` 以及`===`。
{% asset_img fig1.png %}

<!--more-->

## 令人误解的JS 表达式
1.
```js
[] + {} // '[object Object]'
{} + [] // 0
```
**错误的理解：**
`+` 操作符会根据第一个操作数是`[]` 还是`{}` 而给出不同的结果

**正确的解释：**
第一行中，`{}` 出现在`+` 操作符的表达式中，因此被翻译为一个实际的值（一个空的`object`）。`[]` 被强制转换为`""`，因此`{}` 也会被强制转换为一个`string`：`[object Object]`。

第二行中，`{}` 被翻译为一个独立的`{}` 空代码块（它什么也不做）。块儿不需要分号来终结它们，所以这里缺少分号不是一个问题。最终，`+[]` 是一个将`[]` 明确强制转化为`number` 的表达式，而它的值是`0`。

## 参考文档
1. [你不懂JS：类型与语法](https://github.com/JobbyM/You-Dont-Know-JS/blob/1ed-zh-CN/types%20%26%20grammar/ch4.md)
