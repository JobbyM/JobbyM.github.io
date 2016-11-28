---
title: JS Array reduce
date: 2016-11-24 17:39:26
comments: true
tags:
  - 技术
  - JS Array Function
  - reduce
  - redux
categories: 技术
---

## 介绍

1. 对数组中的所有元素调用指定的回调函数。该回调函数的返回值为累积结果，并且此返回值在下一次调用该回调函数时作为参数提供。

<!--more-->

## 语法

```js
array1.reducer(callbackfn[, initalValue1])
```

## 参数

参数 | 定义
---|---|---
array1 | 必须。一个数组对象
callbackfn | 必须。一个接受最多四个参数的函数。对于数组中的每个元素，reduce 方法都会调用callbackfn 函数一次
initialValue | 可选。如果指定initialValue， 则它将用作初始值来启动累积。第一次调用callbackfn 函数会将此值作为参数而非数组值提供。

## 返回值

1. 通过最后一次调用回调函数获得的累积结果。

## 异常

1. 当满足下列任一条件时，将引发`TypeError` 异常
	1. `callbackfn` 参数不是函数对象
	2. 数组不包含元素，且未提供`initialValue`

## 备注

1. 如果提供了`initialValue`，则`reduce` 方法会对数组中的每个元素调用一次`callbackfn` 函数（按升序索引顺序）。如果未提供`initialValue`，则`reduce` 方法会对第二个元素开始的每个元素调用`callbackfn` 函数。
2. 回调函数的返回值在下一次调用回调函数时作为`previousValue` 参数提供。最后一次调用回调函数获得的返回值为reduce` 方法的返回值。
3. 不为数组中缺少的元素调用该回调函数。

### 回调函数语法

1. 回调函数的语法如下：
```js
function callbackfn(previousValue, currentValue, currentIndex, array1)
```

回调参数 | 定义
previousValue | 通过上一次调用回到函数获得的值。如果向`reduce` 方法提供`initialValue` ，则在首次调用函数时，`previousValue` 为`initalValue`
currentValue | 当期数组元素的值
currentIndex | 当前数组元素的数字索引
array1 |  包含该元素的数组对象

### 示例

1. 下面的示例将数组值连接成字符串，各个值用“::”分割开。由于未向`reduce` 方法提供初值，第一次调用回调函数时会将"abc" 作为`previousValue` 参数并将“def”作为`currentValue` 参数。
```js
// Define the callback function.
function appendCurrent (previousValue, currentValue) {
    return previousValue + "::" + currentValue;
    }

// Create an array.
var elements = ["abc", "def", 123, 456];

// Call the reduce method, which calls the callback function
// for each array element.
var result = elements.reduce(appendCurrent);

document.write(result);

// Output:
//  abc::def::123::456

```
2. 统计一个数组中有多少个不同的单词
```js
// Define the callback function.
function getWordCount (previousValue, currentValue) {
    previousValue[currentValue] = (previousValue[currentValue] + 1) || 1;
    return previousValue;
    }

// Create an array.
var elements = ["apple", "orange", "apple", "orange", "pear", "orange"];

// Call the reduce method, which calls the callback function
// for each array element.
var result = elements.reduce(appendCurrent, {});

console.log(result);

// Output:
//  {apple: 2, orange: 3, pear: 1}
```

## 参考文档

1. [JS 的内建函数reduce](http://www.cnblogs.com/accordion/p/5230287.html)
2. [MSDN reduce 方法](https://msdn.microsoft.com/library/ff679975(v=vs.94).aspx)
3. [5个数据Array方法:indexOf、filter、forEach、map、reduce使用实例](http://www.jb51.net/article/60502.htm)
