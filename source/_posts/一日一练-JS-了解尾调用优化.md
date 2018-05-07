---
title: 一日一练-JS 了解尾调用优化
date: 2018-04-28 10:14:29
tags:
  - 技术
  - 一日一练
  - JS
categories: 技术
---

> 子曰：尾调用-> 尾调用优化
> 下面大部分内容来自[ESMAScript 6 入门 尾调用优化 阮一峰](http://es6.ruanyifeng.com/#docs/function#%E5%B0%BE%E8%B0%83%E7%94%A8%E4%BC%98%E5%8C%96)

## 什么是尾调用
尾调用（Tail Call）是函数式编程的一个重要概念，就是指某个函数的最后一步是调用另一个函数。
```js
function foo (x) {
  return bar (x)
}
```
上面代码中，函数`foo` 的最后一步是调用函数`bar`，这就叫尾调用。

<!--more-->

## 尾调用优化
尾调用优化（Tail Call Optimization，TCO）是一个ES6 要求的优化机制，它会使一些在JS 中不可能的递归模式变得可能。TCO 允许一个位于另一个函数的尾部位置的函数调用不需要额外的资源就可以执行，这意味着引擎不再需要对递归算法的调用栈深度设置一个随意的限制了。

我们知道，函数调用会在内存形成一个“调用记录”，又称“调用帧”（call frame），保存调用位置和内部变量等信息。如果在函数`A` 的内部调用函数`B`，那么在`A` 的调用帧上方，还会形成一个`B` 的调用帧。等到`B` 运行结束，将结果返回`A`，`B` 的调用帧才会消失。如果函数`B` 内部还调用函数`C`，那就还有一个`C` 的调用帧，以此类推。所有的调用帧，就形成一个“调用栈”（call stack）。

尾调用由于是函数的最后一步操作，所以不需要保留外层函数的调用帧，因为调用位置、内部变量等信息都不会再用到了，所以直接用内层函数的调用帧，取代外层函数的调用帧就可以了。
```js
function foo () {
  let m = 1
  let n = 2
  return bar(m + n)
}

foo()

// 等同域
function foo () {
  return bar(3)
}

// 等同于
bar(3)
```
上面代码中，如果函数`bar` 不是尾调用，函数`foo` 就需要保存内部变量`m` 和`n` 的值、`bar` 的调用信息等。但由于调用`bar` 之后，函数`foo` 就结束了，所以执行到最后一步，完全可以删除`foo(x)` 的调用帧，只保留`bar(3)` 的调用帧。

这就叫做“尾调用优化”（Tail call optimization），即只保留内存函数的调用帧。如果所有函数都是尾调用，那么完全可以做到每次执行时，调用帧只有一项，这将大大节省内存。这就是"尾调用优化" 的意义。

注意，只有不再用到外层函数的内部变量，内存函数的调用帧才会取代外层函数的调用帧，否则就无法进行“尾调用优化”。
```js
function addOne (a) {
  var one = 1
  function inner (b) {
    return b + one
  }
  return inner(a)
}
```
上面的函数不会进行尾调用优化，因为内存函数`inner` 用到了外层函数`addOne` 的内部变量`one`。

## 参考文档
1. [ECMAScript 6 入门 阮一峰](http://es6.ruanyifeng.com/#docs/function)
2. [尾调用优化 阮一峰](http://www.ruanyifeng.com/blog/2015/04/tail-call.html)
2. [你不了解的JS 尾部调用优化（TCO）](https://github.com/JobbyM/You-Dont-Know-JS/blob/1ed-zh-CN/async%20%26%20performance/ch6.md#%E5%B0%BE%E9%83%A8%E8%B0%83%E7%94%A8%E4%BC%98%E5%8C%96-tco)
