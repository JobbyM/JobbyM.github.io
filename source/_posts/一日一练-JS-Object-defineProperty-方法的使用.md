---
title: 一日一练-JS Object.defineProperty 方法的使用
date: 2018-04-23 08:44:25
comments: true
tags:
  - 技术
  - 一日一练
  - JS
categories: 技术
---

> 子曰：学好Object.defineProperty，你也可以写一个Vuejs
> 大部分内容参考自[Microsoft Docs](https://docs.microsoft.com/zh-cn/scripting/javascript/reference/object-defineproperty-function-javascript)

Vuejs 的实现中用到了`Object.defineProperty()` 方法，因此在这里系统的了解了一下此方法。

## 作用
将属性添加到对象，或修改现有属性的特性。

## 语法
```
Object.defineProperty(object, propertyname, descriptor)
```

## 参数
`object`
必需。要在其上添加或修改属性的对象。这款有是一个本地JavaScript 对象（即用户定义的对象或内置对象）或DOM 对象。
`propertyname`
必需。一个包含属性名称的字符串。
`descriptor`
必需。属性描述符。它可以针对数据属性或访问器属性。

## 返回值
已修改对象

<!--more-->

## 备注
可使用`Object.defineProperty` 函数来执行以下操作：
* 向对象中添加新属性。
* 修改现有属性的特性。
  描述符对象中会提供属性定义，用于描述数据属性或访问器属性的特性。描述符对象是`Object.defineProperty` 函数的参数。
  若要将多个属性添加到对象，或修改现有的多个属性，可以使用[Object.defineProperties()](https://docs.microsoft.com/zh-cn/scripting/javascript/reference/object-defineproperties-function-javascript)

## 异常
如果以下任意条件为`true`，则引发`TypeError` 异常：
* `object` 参数不是对象
* 对象不是可扩展，且不存在指定的属性名称
* `descriptor` 具有`value` 或`writable` 特性，并且具有`get` 或`set` 特性。
* `descriptor` 具有`get` 或`set` 特性，上述特性不是函数且已定义。
* 指定的属性名称已存在，现有属性具有`false` 的`configurable` 特性，且`descriptor` 包含一个或多个与现有属性中特性不同的特性。但是，当现有属性具有`false` 的`configurable` 特性和`true` 的`writable` 特性时，则允许`value` 或`writable` 特性不同。


## 添加数据属性
以下示例中，`Object.defineProperty` 函数向用户定义的对象添加数据属性。
```js
var newLine = '<br />'

// 创建一个用户定义对象
var obj = {}

// 向对象中添加数据属性
Object.defineProperty(obj, 'newDataProperty', {
  value: 101,
  writable: true,
  enumerable: true,
  configurable: true
})


// 设置属性值
obj.newDataProperty = 102
document.write('Property value: ' + obj.newDataProperty)

// 输出
// Property value: 102
```
若要列出对象属性，请将以下代码添加到此示例中。
```js
var names = Object.getOwnPropertyNames(obj)
for (var i = 0; i < names.length; i ++) {
  var prop = names[i]

  document.write(prop + ': ' + obj[prop])
  document.write(newLine)
}

// 输出
// newDataProperty: 102
```

## 修改数据特性

若要修改对象的属性特性，请将以下代码添加到前面所示的`addDataProperty` 函数。`descriptor` 参数只包含`writable` 特性。其他数据属性特性保持不变。
```js
// 修改属性的writable 特性
Object.definProperty(obj, 'newDataProperty', { writable: false })

// 使用descriptor 列出属性特性
// 使用Object.getOwnPropertyDescriptor 获得descriptor
var descriptor = Object.getOwnPropertyDescriptor(obj, 'newDataProperty')
for (var prop in descriptor) {
  document.write(prop + ': ' + descriptor[prop])
  document.write(newLine)
}

// 输出
// writable: false
// value: 102
// configurable: true
// enumerable: true
```

## 添加访问器属性

在以下示例中，`Object.defineProperty` 函数向用户定义的对象添加访问器属性。
```js
var newLine = '<br />'

// 创建一个用户定义对象
var obj = {}

// 向对象中添加访问器属性
Object.defineProperty(obj, 'newAccessorProperty', {
  set: function (x) {
    document.write('in property set accessor' + newLine)
    this.newaccpropvalue = x
  },
  get: function () {
    document.write('in property get accessor' + newLine)
    return this.newaccpropvalue
  },
  enumerable: true,
  configurable: true
})

// 设置属性值
obj.newAccessorProperty = 30
document.write('Property value: ' + obj.newAccessorProperty + newLine)

// 输出：
// in property set accessor
// in property get accessor
// Property value: 30
```
若要列出对象属性，请将以下代码添加到此示例中。
```js
var names = Object.getOwnPropertyNames(obj)
for (var i = 0; i < names.length; i ++) {
  var prop = names[i]

  document.write(prop + ': ' + obj[prop])
  document.write(newLine)
}

// 输出：
// in property get accessor
// newAccessorProperty: 30
````

## 修改访问器属性
若要修改对象的访问器属性，请将以下代码添加前面所示的代码。`descriptor` 参数只包含`get` 访问器定义。其他属性特性保持不变。
```js
// 修改get 访问器
Object.defineProperty(obj, 'newAccessorProperty', {
  get: function () { return this.newaccpropvalue }
})

// 使用descriptor 列出属性特性
// 使用Object.getOwnPropertyDescriptor 获得descriptor
var descriptor = Object.getOwnPropertyDescriptor(obj, 'newAccessorProperty')
for (var prop in descriptor) {
  document.write(prop + ': ' + descriptor[prop])
  document.write(newLine)
}

// 输出：
// get: function () { return this.newaccpropvalue }
// set: function (x) { document.write('in property get accessor' + newLine) return this.newaccpropvalue}
// configurable: true
// enumerable: true
```

## 修改DOM 元素上的属性
下面的示例演示如何通过使用`Object.getOwnPropertyDescriptor` 函数来获取和修改属性的属性描述符，从而自定义内置DOM 属性。对于此示例中，必须通过使用ID 为"div" 的DIV 元素。
```js
// 获取querySelector 属性的descriptor
var descriptor = Object.getOwnPropertyDescriptor(Element.prototype, 'querySelector')

// 将属性设置为只读
descriptor.value = 'query'
descriptor.writable = false
// 将其应用到Element prototype
Object.defineProperty(Element.prototype, 'querySelector', descriptor)

// 从HTML body 中获得DOM 元素
var elem = document.getElementById('div')

// 尝试改变值。这会导致修改的值属性被调用。
elem.querySelector = 'anotherQuery'
document.write(elem.querySelector)

// 输出：
// query
```
