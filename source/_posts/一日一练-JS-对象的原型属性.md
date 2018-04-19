---
title: 一日一练-JS 对象的原型属性
date: 2018-04-19 16:45:32
tags:
  - 技术
  - 一日一练
  - JS
categories: 技术
---

> 子曰：JS 啊，全是原型
> 下面相关知识点来自《JavaScript 权威指南（第六版）（中文版）》6.8 对象的三个属性

每一个JavaScript 对象都有与之相关的原型（prototype）、类（class）和可扩展性（extensible attribute）这三个属性。
> 子曰：原型是JavaScript 对象一个属性

对象的原型属性是用来继承属性的，这个属性如此重要，以至于我们经常把“o 的原型属性” 直接叫做“o 的原型”。
> 子曰：原型的重要性，或原型是用来干什么的

原型属性是在实例对象创建之处就设置好的。例如，通过对象直接量创建的对象使用`Object.prototype` 作为它们的原型。而`Object.prototype` 在使用对象直接量创建对象之前就已经存在了。
{% asset_img prototype_1.jpg %}
> 子曰：原型是什么时候产生的



在ECMAScript 5 中，将对象作为参数传入`Object.getPrototypeOf()` 看查询它的原型。在ECMAScript 3 中，则没有与之等价的函数，但经常使用表达式`o.constructor.prototype` 来检测一个对象的原型。
> 子曰：如何查询原型

通过new 表达式创建的对象，通常继承一个`constructor` 属性，这属性指代创建这个对象的构造函数。`constructor.prototype` 才是对象直接量的真正原型，但对于通过`Object.create()` 创建的对象则往往不是这样。
{% asset_img prototype_5.png %}
> 子曰：还是如何表示原型，但是提供一个`constructor` 属性

想要检测一个对象是否是另一个对象的原型（或处于原型链中），请使用`isPrototypeOf()` 方法。例如，可以通过`p.isPrototypeOf(o)` 来检测p 是否是o 的原型：
```js
var p = {x:1}                     // 定义一个原型对象
var o = Object.create(p)          // 使用这个原型对象创建一个对象
p.isPrototypeOf(o)                // => true: o 继承自p
Object.prototype.isPrototypeOf(o) // => true: p 继承自Object.prototype
```
> 子曰：如何检测一个对象是否是另一个对象的原型

Mozilla 实现的JavaScript 对外暴露了一个专门命名为`__proto__` 的属性，用于直接查询/设置对象的原型。但并不推荐使用`__proto__`。
> 子曰：为什么使用`__proto__`，`__proto__` 的由来
