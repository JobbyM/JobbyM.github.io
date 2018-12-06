---
title: 一日一练-JS 对象属性遍历的5 中方法
date: 2018-12-04 09:43:57
tags:
  - 技术
  - 一日一练
  - JS
categories: 技术
---

本文摘自[ECMAScript 6 入门 ruanyifeng](http://es6.ruanyifeng.com/#docs/object)，有部分删节，看原文请到原地址。

ES6 一共有5 种方法可以遍历对象的属性。

**（1）for...in**
`for...in` 循环遍历对象自身的和继承的可枚举属性（不含Symbol 属性）。

**（2）Object.keys(obj)**
`Object.keys` 返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含Symbol 属性）的键名。

**（3）Object.getOwnPropertyNames(obj)**
`Object.getOwnPropertyNames` 返回个数组，包含对象自身的所有属性（不含Symbol 属性，但是包括不可枚举属性）的键名。

**（4）Object.getOwnPropertySymbols(obj)**
`Object.getOwnPropertySymbols`返回一个数组，包含对象自身的所有Symbol 属性的键名。

**（5）Reflect.ownKeys(obj)**
`Reflect.ownKeys` 返回一个数组，包含对象自身的所有键名，不管键名是Symbol 或字符串，也不管是否可枚举。

以上的5 种方法遍历对象的键名，都遵守同样的属性遍历的次序规则。

* 首先遍历所有数值键，按照数值升序排列。
* 其次遍历所有字符串键，按照加入时间升序排列。
* 最后遍历所有Symbol 键，按照加入时间升序排列。

```js
Reflect.ownKeys({[Symbol()]:0, b:0, 10:0, 2:0, a:0})
// ['2', '10', 'b', 'a', Symbol()]
```

上面代码中，`Reflect.ownKeys` 方法返回一个数值，包含了参数对象的所有属性。这个数组的属性次序是这样的，首先是数值属性`2` 和`10`，其次是字符串属性`b` 和`a`，最后是Symbol 属性。
