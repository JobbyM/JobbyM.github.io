---
title: 一日一练-JS 了解typeof 和instanceOf
date: 2018-04-28 16:54:39
tags:
---

JavaScript 拥有带类型的值，没有带类型的变量。**变量可以在任何时候，持有任何值。**
基本（primitive）类型
1. null
2. undefined
3. boolean
4. number
5. string
6. object
7. symbol -- 在ES6 中被加入的

使用`typeof` 可以判断一个值的类型是什么，而且总是返回七种字符串值（**注意，类型是字符串**）中的一种。
```js
var a = null
typeof a          // 'object'

var a
typeof a          // 'undefined'

var a = true
typeof a          // 'boolean'

var a = 1   
typeof a          // 'number'

var a ='hello world'
typeof a          // 'string'

var a = {b: 'c'}
typeof a           // 'object'

var a = Symbol()   
typeof a           // 'symbol'

var a = []
typeof a           // 'object'

var a = function () {}
typeof a           // 'function'
```

注意：
1.如果你想要使用`null` 类型来测试`null` 值，你需要一个复合条件：
```js
var a = null
(!a && typeof a === 'object') // true
```
2.可以看到`typeof` 可以返回`function`。
```js
typeof function a () { /* ... */} === 'function' // true
```
但是`function` 不是JS 中的一种顶层的内建类型。它实际上是对象(object) 的“子类型”。特别地，一个函数（function）被称为“可调用对象” -- 一个拥有`[[Call]]` 内部属性，运行被调用的对象。
3.对于数组
```js
typeof [] === 'object' // true
```
它也仅仅是对象。将它们认为是对象的“子类型”，带有被数字索引的附加性质（与仅仅使用字符串键的普通对象相反），并维护着一个自动更新的`.length` 属性。
4.注意“undefined” 与“undeclared” 的区别
```js
var a
a // undefined
b // ReferenceError: b is not defined
```
一个“undefined” 变量是在可访问的作用域中已经被声明过的，但是在 **这个时刻** 它里面没有任何值。相比之下，一个“undeclared” 变量是在可访问的作用域中还没有被正式声明的。
“undefined” 同“is not defined” 是非常不同的。
5.当`typeof` 一个未被声明的变量的返回值
```js
var a

typeof a // "undefined"
typeof b // "undefined"
```
`typeof` 操作符甚至为“undeclared”（或“not defined”）变量返回`'undefined'`。当我们执行`typeof b` 时，即使`b` 是一个未声明变量，也不会有错误被抛出。这是`typeof` 的一种特殊的安全防卫行为。


## 参考文档
1. [你不了解的JS  入门与进阶](https://github.com/JobbyM/You-Dont-Know-JS/blob/1ed-zh-CN/up%20%26%20going/ch2.md#%E5%80%BC%E4%B8%8E%E7%B1%BB%E5%9E%8B)
2. [深入理解javascript之typeof和instanceof CSDN](https://blog.csdn.net/mevicky/article/details/50353881)
