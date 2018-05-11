---
title: 一日一练-JS 了解this
date: 2018-04-28 18:40:24
tags:
  - 技术
  - 一日一练
  - JS
categories: 技术
---

> 子曰：this 是什么？

## 什么是`this`
`this` 不是编写时绑定，而是运行时绑定。它依赖于函数调用的上下文条件。`this` 绑定与函数声明的位置没有任何关系，而于函数被调用的方式紧密相连。

当一个函数被调用时，会建立一个称为执行环境的活动记录。这个记录包含函数是从何处（调用--`call-stak`）被调用的，韩式是如何被调用的，被传递了什么参数等信息。这个记录的属性之一，就是函数执行期间将被使用的`this` 引用。

`this` 实际上是在函数被调用时建立的一个绑定，它指向什么是完全由函数被调用的调用点（`call-site`）来决定的。

<!--more-->

## 调用栈（call-stack）
使我们到达当前执行位置而被调用的所用方法的堆栈。

## 调用点（call-site）
1. 函数在代码中被调用的位置（不是被声明的位置）。
2. 调用点位于当前执行中的函数之前的调用

展示调用栈和调用点：
```js
function baz () {
  // 调用栈是: `baz`
  // 我们的调用点是global scope（全局作用域）

  console.log('baz')
  bar() // <-- `baz` 调用点
}

function bar () {
  // 调用栈是：`baz` -> `bar`
  // 我们的调用点位于`baz`

  console.log('bar')
  foo() // <-- `foo` 的调用点
}

function foo () {
  // 调用栈是：`baz` -> `bar` -> `foo`
  // 我们的调用点位于`bar`

  console.log('foo')
}

baz() // <-- `baz` 的调用点
```

## 判断调用点的四种规则
### 1. 默认绑定（Default Binding）
独立函数调用
```js
function foo () {
  console.log(this.a)
}

var a = 2

foo() // 2
```
上述为非严格模式，因此`this` 为全局对象，而在严格模式下（`strict mode`）下，`this` 为`undefined`
```js
function foo () {
  'use strict'
  console.log(this.a)
}

var a = 2

foo() // TypeError: `this` is `undefined`
```

### 2. 隐含绑定（Implicit Binding）
调用点是否有一个环境对象（content object），也称为拥有者（owning）或容器（containing）对象。
```js
function foo () {
  console.log(this.a)
}

var obj = {
  a: 2,
  foo: foo
}

obj.foo() // 2
```
首先`foo()` 被声明然后作为引用属性添加到`obj`。调用点使用`obj` 环境来 **引用** 函数，也就是说`obj` 对象在函数被调用的时间点上“拥有”或“包含” 这个 **函数引用**。
在`foo()` 被调用的位置上，它被冠以一个指向`obj` 的对象引用。当一个方法引用存在一个环境对象时，**隐含绑定** 规则会说：这个环境对象应当被用于这个函数调用的`this` 绑定。

因为`obj` 是`foo()` 调用的`this`，所以`this.a` 就是`obj.a` 的同义词。
还要注意：只有对象属性引用链的最后一层是影响调用点的。

**隐含丢失**
当一个`隐含绑定` 丢失了它的绑定，它通常会退回`默认绑定`，根据`strict mode` 的状态，其结果不是全局对象就是`undefined`。
```js
function foo () {
  console.log(this.a)
}

var obj = {
  a: 2,
  foo: foo
}

var bar = obj.foo // 函数引用
var a = 'oops, global' // `a` 也是一个全局对象的属性
bar() // 'oops, global'
```
尽管`bar` 似乎是`obj.foo` 的引用，但实际上它只是另一个`foo` 本身的引用而已。起作用的调用点是`bar()`，因此 **默认绑定** 适用于这里。

考虑传递一个回调函数：
```js
function foo () {
  console.log(this.a)
}

function doFoo (fn) {
  // `fn` 只不过是`foo` 的另一个引用
  fn() // <-- 调用点
}

var obj = {
  a: 2,
  foo: foo
}

var a = 'oops, global' // `a` 也是一个全局对象的属性
doFoo(obj.foo) // 'oops, global'
```
参数传递仅仅是一种隐含的赋值，而且因为我们在传递一个函数，它是一个隐含的引用赋值，所以最终结果前一个代码段一样。

如果接受传递回调的函数是语言内建的，也是一样的。
```js
function foo () {
  console.log(this.a)
}

var obj = {
  a: 2,
  foo: foo
}

var a = 'oops, global' // `a` 也是一个全局对象的属性

setTimeout(obj.foo, 100) // 'oops, global'
```
下面的`setTimeout()` 实现，等同于JavaScript 环境内建的实现：
```js
function setTimeout(fn, delay) {
  // （通过某种方法） 等待`delay` 毫秒
  fn() // <-- 调用点
}
```
注意：事件处理器强制回调的`this` 指向触发事件的DOM 元素。

### 3. 明确绑定（Explicit Binding）
使用函数的`call(...)` 和`apply(..)` 方法，强制一个函数调用使用某个特定对象作为`this` 绑定。它们接收的第一个参数都是一个用于`this` 的对象，之后使用这个指定的`this` 来调用函数。因为明确指定`this` 是什么，所以我们称这种方式为 **明确绑定（explicit binding）**。
```js
function foo () {
  console.log(this.a)
}

var obj = {
  a: 2
}
foo.call(obj) // 2
```
通过`foo.call(..)` 使用 **明确版定** 来调用`foo`，允许我们强制函数的`this` 指向`obj`。

靠 **明确绑定**  不能解决函数“丢失” 自己原本的`this` 绑定、被第三方框架覆盖等问题。

**硬绑定（Hard Binding）**
```js
function foo (something) {
  console.log(this.a, something)
  return this.a + someting
}

// 简单的`bind` 帮助函数
function bind (fn, obj) {
  return function () {
    return fn.apply(obj, arguments)
  }
}

var obj = {
  a: 2
}
var bar = bind(foo, obj)

var b = bar(3) // 2 3
console.log(b) // 5
```
ES5 提供`Function.prototype.bind` ，像这样使用：
```js
function foo (something) {
  console.log(this.a, something)
  return this.a + someting
}

var obj = {
  a: 2
}
var bar = foo.bind(obj)

var b = bar(3) // 2 3
console.log(b) // 5
```
`bind(..)` 返回一个硬编码的新函数，它使用你指定的`this` 环境来调用原本的函数。

**API 调用的“环境”**
许多库中的函数，和许多在JavaScript 语言以及宿主环境中的内建函数，都提供一个可选参数，通常称为“环境（context）”，这种设计作为一种替代方案来确保你的回调函数使用特定的`this` 而不必非得使用`bind(..)`。
```js
function foo (el) {
  console.log(el, this.id)
}

var obj = {
  id: 'awesome'
}

// 使用`obj` 作为`this` 来调用`foo(..)`
[1, 2, 3].forEach(foo, obj)   // 1 awesome 2 awesome 3 awesome
```

### 3. `new` 绑定（`new` Binding）
`new` 是函数调用可以绑定`this` 的最后一种方式，我们称之为 **new 绑定(new binding)**
```js
function foo (a) {
  this.a = a
}

var bar = new foo(2)
console.log(bar.a)
```
通过在前面使用`new` 来调用`foo(..)`，我们构建了一个新的对象并把这个新对象作为`foo(..)` 调用的`this`。

## 判断`this`
1. 函数是通过`new` 被调用的吗（**new 绑定**）？如果是，`this` 就是新构建的对象。
```js
var bar = new foo()
```
2. 函数是通过`call` 或`apply` 被调用（**明确绑定**），设置是隐藏在`bind` 硬绑定之中吗？如果是，`this` 就是那个被明确指定的对象
```js
var bar = foo.call(obj2)
```
3. 函数是通过环境对象（也称为拥有者或容器对象）被调用的吗（**隐含绑定**）？如果是，`this` 就是那个环境对象。
```js
var bar = obj1.foo()
```
4. 否则，使用默认的`this`（**默认绑定**）。如果在`strict mode` 下，就是`undefined`，否则是`global` 对象。
```js
var bar = foo()
```

## 特殊规则
**词法`this`**
ES6 引入了箭头函数（arrow-function），箭头函数不是通过`function` 关键字声明的，而是通过所谓的“大箭头”操作符：`=>`。箭头函数从封闭它的（函数或全局）作用域采用`this` 绑定。
下面是箭头函数的词法作用域：
```js
function foo () {
  // 返回一个箭头函数
  return (a) => {
    // 这里的`this` 是从词法上从`foo()` 采用的
    console.log(this.a)
  }
}

var obj1 = {
  a: 2
}

var obj2 = {
  a: 3
}

var bar = foo.call(obj1)
bar.call(obj2) // 2, 不是3
```
在`foo()` 中创建的箭头函数在词法上捕获`foo()` 被调用时的`this`，不管它是什么。因为`foo()` 被`this` 绑定到`obj1`，`bar`（被返回的箭头函数的一个引用）也将被`this` 绑定到`obj1`。一个箭头函数的词法绑定是不能被覆盖的（就连`new` 也不行）。


## 参考文档
1. [你不了解的JS  入门与进阶](https://github.com/JobbyM/You-Dont-Know-JS/blob/1ed-zh-CN/up%20%26%20going/ch2.md#this-%E6%A0%87%E8%AF%86%E7%AC%A6)
