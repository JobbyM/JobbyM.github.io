---
title: 一日一练-JS 什么是原型
date: 2018-04-18 16:56:01
tags:
  - 技术
  - 一日一练
  - JS
categories: 技术
---

> 子曰：道生一，一生二，二生三，三生万物
> 下面相关知识点来自《JavaScript 权威指南（第六版）（中文版）》6.1.3 原型，配图为使用[canvas.qq.com](http://canvas.qq.com/) 自己创建的

每一个JavaScript 对象（null 除外）都和另一个对象相连。另一个对象就是我们熟知的原型，每一个对象都从原型继承属性。
{% asset_img prototype_0.jpg %}
所有通过对象直接量创建的对象，都具有同一个原型对象，并可通过JavaScript 代码`Object.prototype` 获得原型对象的引用。
{% asset_img prototype_1.jpg %}
通过关键字`new` 和构造函数调用创建的对象的原型就是构造函数的`prototype` 属性的值。因此，同使用`{}` 创建对象一样，通过`new Object()` 创建的对象也继承自`Object.prototype`。同样，通过`new Array()` 创建的对象的原型就是`Array.prototype`.
{% asset_img prototype_2.jpg %}
{% asset_img prototype_3.jpg %}
没有原型的对象为数不多，`Object.prototype` 就是其中之一。它不继承任何属性。
{% asset_img prototype_0.jpg %}
其他原型对象都是普通对象，普通对象都具有原型。所有内置构造函数（以及大部分自定义的构造函数）都具有一个继承自`Object.prototype` 的原型。例如，`Date.prototype` 的属性继承自`Object.prototype`，因此由`new Date()` 创建的Date 对象的属性同时继承自`Date.prototype` 和`Object.prototype`。这一系列链接的原型对象就是所谓的“原型链（prototype chain）”
{% asset_img prototype_4.jpg %}
