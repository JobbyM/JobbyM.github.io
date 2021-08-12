---
title: '一日一练-JS sum(1,2)(3)函数柯里化'
date: 2021-08-12 16:35:49
tags:
  - 技术
  - 一日一练
  - JS
  - 柯里化
  - 转载
categories: 技术
---

本文转载自[面试题-sum(1,2)(3)函数柯里化](https://www.jianshu.com/p/54a532c2f556)，有部分删节，看原文请到原地址。

```
题目 
编写一个方法sum，使sum(1,2,3) 和 sum(1,2)(3)的输出都为 6
```

## 前言
柯里化，英语：Currying，是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术。

<!--more-->

```js
// 普通的 add 函数
function add (x, y) {
    return x + y
}

// Currying 后
function curryingAdd (x) {
    return function (y) {
        return x + y
    }
}

add(1, 2) // => 3
curryingAdd(1)(2) // => 3
```

## 思考
针对这个题目，需要做的是：
1、利用闭包创建一个数组保存参数
2、返回一个方法，用于接收下一个括号里的参数
3、全部接收后，返回所有参数的和

## 代码
### 不限定参数数量
```js
// 不限数量参数
function sum () {
    // 第一次执行时，定义一个数组专门用来存储所有的参数
    var _args = Array.prototype.slice.call(arguments)

    // 在内部声明一个函数，利用闭包的特性保存 _args 并收集所有的参数值
    var _adder = function () {
        _args.push(...arguments)
        
        return _adder
    }

    // 利用 toString 隐式转换的特性，当最后执行时隐式转换，并计算最终的值返回
    _adder.toString = function () {
        let sum = _args.reduce(function (a, b) {
            return a + b
        }, 0)
        return sum
    }
    return _adder
}

console.log(sum(1)(2)(3))     // => f 6
console.log(sum(1, 2, 3)(4))   // => f 10
console.log(sum(1)(2)(3)(4)(5)) // => f 15
```

以上可以实现不限定参数数量的 sum 函数。

其中 `Array.prototype.slice.call` 和 `toString` 隐式转换其实都是 js 基础知识点。

但是这里先明白其功能
1. 一个是将参数转换为数组。
2. 一个是在返回最后一次执行结束后，return 了一个 _adder 方法，在输出时，正常会隐式调用Function.toString 的方法，以字符方式输出方法。因为上面改写了 _adder 的 `toString` 方法，所以最后没有以字符方式输出方法，而是隐式调用了我们改写的输出了所有参数的和的 `toString` 方法。

这样的方法在 chrome 可以输出 `f 6` 这样的数字，但如果输出一下这个结果的类型比如

```js
// ...上略
console.log(typeof sum(1)(2)(3))              // function
```

我们会得到这个结果的类型其实还是 function。所以这个方法其实是很勉强的实现了题目的效果，但得到的数据不能直接作为 Number 来使用。
还是需要显示调用才能得到 number 类型的结果——

```js
// ...上略
let sumToNumber = Number.parseInt(sum(1)(2)(3))
console.log(sumToNumber)              // 6
console.log(typeof sumToNumber)              // number
```

### 扩展 ES6 改写
使用 ES6 的不定参数 rest 的新特性，该特性可以让不定参数变成一个数组传入，不需要访问 arguments，也省去了使用 `Array.prototype.slice.call(arguments);` 生成数组，更加优雅。

```js
function sumWithES6(...rest) {
    var _args = rest;

    var _adder = function (...innerRest) {
        _args.push(...innerRest); // 这里使用的是ES6数组的解构
        return _adder;
    };

    _adder.toString = function () {
        let sum = _args.reduce(function (a, b) {
            return a + b;
        });
        return sum;
    };
    return _adder;
}

console.log(sumWithES6(1)(2)(3)); // => f 6   f
```

