---
title: 一日一练-JS 实现深拷贝
date: 2021-06-03 16:35:25
tags:
  - 技术
  - 一日一练
  - JS
  - 深拷贝
categories: 技术
---


## 什么是深拷贝
深拷贝是将一个对象从内存中完整的拷贝一份出来，从堆内存中开辟一个新的区域存放新对象，且**修改新对象不会影响原对象**

<!--more-->

## 深拷贝的实现方式
### 1.JSON.parse(JSON.stringify())
```js
let arr = [1, 3, {
    username: 'Jobbym'
}]
let arr2 = JSON.parse(JSON.stringify(arr))
arr2[2].username = 'Jerry'
console.log(arr, arr2)
```
{% asset_img 1.png %}

这是利用了 `JSON.stringify` 将对象转换为 JSON 字符串，再利用 `JSON.parse` 把字符串解析成对象，新的对象产生了，而且对象会开辟新的堆，实现深拷贝。

**这种方法虽然可以实现数组或对象深拷贝，但不能处理函数和正则**，因为这两者基于 `JSON.stringify` 和 `JSON.parse` 处理后，得到的正则就不再是正则了（变为空对象），得到的函数就不再是函数了（变为 null 了）。

如下：
```js
let arr = [1, 3, {
    username: 'Jobbym'
}, function () {}, /\*/]
let arr2 = JSON.parse(JSON.stringify(arr))
arr2[2].username = 'Jerry'
console.log(arr, arr2)
```
{% asset_img 2.png %}

### 2.函数库 lodash 的 _.cloneDeep() 方法
```js
var _ = require('lodash')
var objects = [{ 'a': 1 }, { 'b': 2 }]
 
var deep = _.cloneDeep(objects)
console.log(deep[0] === objects[0])
// => false
```

### 3.jQuery.extend() 方法
jQuery 提供 `$.extend` 实现深拷贝

使用方法, 将第一个参数设置为 true，则 jQuery 返回一个深层次的副本，递归地复制找到的任何对象。
```js
jQuery.extend([deep], target, object1, [objectN])
// deep: 如果设为 true，则递归合并。
// target: 待修改对象。
// object1: 待合并到第一个对象的对象。
// objectN: 待合并到第一个对象的对象。
```

例子
```js
var jsdom = require('jsdom')
var { JSDOM } = jsdom
var { window } = new JSDOM(`<!DOCTYPE html>`)
var $ = require('jquery')(window)
var objects = [{ 'a': 1 }, { 'b': 2 }]
var deep = $.extend(true, {}, objects)
console.log(deep[0] === objects[0])
// => false
```

### 4.手写递归方法
递归方法实现深度拷贝原理：**遍历对象、数组直到里面都是基本数据类型，然后再去复制，就是深度拷贝**
```js
function deepClone(target) {
	if (typeof target === 'object') {
		let cloneTarget = Array.isArray(target) ? [] : {}
		for (const key in target) {
			cloneTarget[key] = deepClone(target[key])
		}
		return cloneTarget
	} else {
		return target
	}
}
let arr = [1, 3, {
    username: 'Jobbym'
}]
let arr2 = deepClone(arr)
arr2[2].username = 'Jerry'
console.log(arr, arr2)
```
{% asset_img 3.png %}

有种特殊情况需注意就是对象存在**循环引用**的情况，即对象的属性直接的引用了自身的情况。

运行这个例子
```js
var target = {
    field1: 1,
    field3: {
        child: 'child'
    },
    field4: [2, 4, 8]
};
target.target = target;

var cloneTarget = deepClone(target)
```

可以看到如下效果
{% asset_img 4.png %}

为了解决循环引用问题，我们可以额外开辟一个存储空间，来存储当前对象和拷贝对象的对应关系，当需要拷贝当前对象时，先去存储空间中找，有没有拷贝过这个对象，如果有的话直接返回，如果没有的话继续拷贝，这样就巧妙化解的循环引用的问题。

这个存储空间，需要可以存储 `key-value` 形式的数据，且 `key` 可以是一个引用类型，我们可以选择 `Map` 这种数据结构：
* 检查 `map` 中有无克隆过的对象
* 有 -- 直接返回
* 没有 -- 将当前对象作为 `key`, 克隆对象作为 `value` 进行存储
* 继续克隆
```js
function deepClone(target, map = new Map()) {
	if (typeof target === 'object') {
		let cloneTarget = Array.isArray(target) ? [] : {}
		if (map.get(target)) {
			return map.get(target)
		}
		map.set(target, cloneTarget)
		for (const key in target) {
			cloneTarget[key] = deepClone(target[key], map)
		}
		return cloneTarget
	} else {
		return target
	}
}
var target = {
    field1: 1,
    field3: {
        child: 'child'
    },
    field4: [2, 4, 8]
};
target.target = target;

var cloneTarget = deepClone(target)
cloneTarget.field4[1] = 333
console.log(target, cloneTarget)
```

结果如下：
{% asset_img 5.png %}


## 参考文档
1.[浅拷贝与深拷贝](https://juejin.cn/post/6844904197595332622)
2.[如何写出一个惊艳面试官的深拷贝](https://juejin.cn/post/6844903929705136141)
