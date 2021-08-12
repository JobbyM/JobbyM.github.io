---
title: 一日一练-JS 数组和类数组的区别
date: 2021-07-27 18:00:07
tags:
  - 技术
  - 一日一练
  - 类数组
categories: 技术
---

## 类数组定义
1. 拥有 length 属性，其它属性（索引）为非负整数（对象中的索引会被当做字符串来处理）
2. 不具有数组所具有的方法

<!--more-->

javascript 中常见的类数组有 `arguments` 对象和 DOM 方法的返回结果。比如 `document.getElementsByTagName()`。

## 类数组转换为数组
### Array.prototype.slice
```js
function toArray(arr){
    return Array.prototype.slice.call(arr)
}
```

### Array.from
```js
function toArray(arr){
    return Array.from(arr)
}
```

## 判断类数组
```js
function foo() {
    var arr = [1,2,3]
    // 1. instanceof
    console.log(arguments instanceof Array)  // false
    console.log(arr instanceof Array)        // true

    // 2. toString()
    console.log(Object.prototype.toString.call(arguments) === '[object Array]') // false
    console.log(Object.prototype.toString.call(arr) === '[object Array]') // true

    // 3. ES 提供的方法Array.isArray()
    console.log(Array.isArray(arguments)) // false
    console.log(Array.isArray(arr))       // true

    // 4. constructor
    console.log(arguments.constructor === Array) // false
    console.log(arr.constructor === Array)       // true
}

foo()
```
## 参考文档
1.[cnblogs js 数组和类数组的区别](https://www.cnblogs.com/jiayeyuan/p/10886568.html)