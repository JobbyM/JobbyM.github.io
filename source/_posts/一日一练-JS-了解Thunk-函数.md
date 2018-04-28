---
title: 一日一练-JS 了解Thunk 函数
date: 2018-04-28 10:14:59
tags:
---

> 子曰：到底什么是thunk？thunk、thunkify、thunkory 还有redux-thunk

## Thunk 的历史
20 世纪60 年代在编程语言刚起步时，计算机科学家研究编译器怎么写比较好（注意，是编译器而不是JavaScript）。**一个争议点是“求值策略”，即函数的参数到底应该合适求值。**
```js
var x = 1

function foo (m) {
  return m * 2
}

f(x + 5)
```
`x + 5` 表达式合适求值？

**1. 传值调用（call by value）**
在进入函数体之前，就计算`x + 5` 的值（等于6），再将这个值传入函数`foo`。C 语言就采用这种策略。
```js
foo(x + 5)
// 传值调用时，等同于
foo(6)
```

**2. 传名调用（call by name）**
直接将表达式`x + 5` 传入函数体，只在用到它的时候求值。Haskell 语言采用这种策略。
```js
foo(x + 5)
// 传名调用时，等同于
(x + 5) * 2
```

编译器的“传名调用” 实现，往往是将参数放到一个临时函数中，在将这个临时函数传入函数体。这个临时函数就叫做Thunk 函数。
```js
var thunk = function () {
  return x + 5
}

function foo (thunk) {
  return thunk() * 2
}
```
**Thunk 函数是“传名调用” 的一种实现策略，用来替换某个表达式。**

**在JavaScript 语言中，Thunk 函数替换的不是表达式，而是多惨函数，将其替换程单参数的版本，且只接受回调函数作为参数**
> 上述来自[Thunk 函数的含义和用法 阮一峰](http://www.ruanyifeng.com/blog/2015/05/thunk.html)。
> 下面则是来自[你不了解的JS Thunks](https://github.com/JobbyM/You-Dont-Know-JS/blob/1ed-zh-CN/async%20%26%20performance/ch4.md#thunks)

## JavaScript 语言的Thunk
1. 狭隘的定义：`thunk` 是一个JS 函数--没有任何参数--它连接并调用另一个函数。

2. 扩展之后的定义：`thunk` 是一个JS 函数--接受一个回调--它连接并调用另一个函数。

对狭隘的定义来说，你用一个函数定义包装函数调用--带着它需要的所有参数--来推迟这个调用的执行，而这个包装用的函数就是`thunk`。当你稍后执行`thunk` 时，你最终会调用那个原始的函数。
下面代码中的`fooThunk` 就是`thunk` 函数。
```js
function foo (x, y) {
  return x + y
}

function fooThunk () {
  return foo(3, 4)
}

// 稍后
console.log(fooThunk()) // => 7
````

而一个接受回调函数的`thunk` 是为了处理一个异步的`thunk` 而产生的。
```js
function foo (x, y, cb) {
  setTimeout(function () {
    cb(x + y)
  }, 1000)
}

function fooThunk (cb) {
  return foo(3, 4, cb)
}

// 稍后
fooThunk(function (sum) {
  console.log(sum) // => 7
})
```
`fooThunk(..)` 仅需要一个`cb(..)` 参数。因为它已经预先制定
了值`3` 和`4`（分别为`x` 和`y`）并准备传递给`foo(..)`。一个`thunk` 只是在外面耐心地等待着它开始工作所需的最后一部分信息：回调。

## Thunkify
使用工具生成`thunk`，而不是手动编码。
```js
function thunkify (fn) {
  var args = [].slice.call(arguments, 1)
  return function (cb) {
    args.push(cb)
    return fn.apply(null, args)
  }
}

var fooThunk = thunkify(foo, 3, 4)

// 稍后
fooThunk(function (sum) {
  console.log(sum)   // => 7
})
```
前面的`thunkify()` 接受`foo(..)` 函数的引用，和任何它所需的参数，并返回`thunk` 本身（`fooThunk(..)`）。然而，这不是你将在JS 中发现的`thunk` 的典型表达方式。

与`thunkify(..)` 制造`thunk` 本身相反，典型的`thunkify(..)` 工具将产生一个制造`thunk` 的函数。
```js
function thunkify (fn) {
  return function () {
    var args = [].slice.call(arguments)
    return function (cb) {
      args.push(cb)
      return fn.apply(null, args)
    }
  }
}
```
不同之处是有一个额外的`return function () {..}`。用法也不一样。
```js
var whatIsThis = thunkify(foo)

var fooThunk = whatIsThis(3, 4)

// 稍后
fooThunk(function (sum) {
  console.log(sum)        // => 7
})
```
`whatIsThis` 不是`thunk`，它是一种`thunk` 的“工厂”，可以称之为`thunkory`（`thunk` + `factory`）。于是`thunkify(..)` 制造了一个`thunkory`，而一个`thunkory` 制造`thunk`。

一般来讲，在程序的一开始就制造一些`thunkory` 来包装既存API 的方法是十分有用的，然后就可以在需要`thunk` 的时候传递并调用这些`thunkory`。这两个分开的步骤保证了功能上更干净的分离。
```js
// 更干净
var fooThunkory = thunkify(foo)

var fooThunk1 = fooThunkory(3, 4)
var fooThunk2 = fooThunkory(5, 6)

// 而这个不干净
var fooThunk1 = thunkify(foo, 3, 4)
var fooThunk2 = thunkify(foo, 5, 6)
```

## 参考文档
1. [Thunk 函数的含义和用法 阮一峰](http://www.ruanyifeng.com/blog/2015/05/thunk.html)
2. [你不了解的JS Thunks](https://github.com/JobbyM/You-Dont-Know-JS/blob/1ed-zh-CN/async%20%26%20performance/ch4.md#thunks)
