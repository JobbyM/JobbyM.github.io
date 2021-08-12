---
title: 一日一练-JS 几道题
date: 2021-08-12 11:25:17
tags:
  - 技术
  - 一日一练
  - 面试题
categories: 技术
---

> 子曰：天将降大任于斯人也，必先苦其心志，劳其筋骨。

## 0x00
```js
function foo() {
    console.log(this.a);
}
var a = 2;
var obj = {
    a: 3,
    foo,
}
var p = {
    a: 4,
}
var other_foo = obj.foo;
obj.foo();   // => 3
other_foo(); // => 2
(p.foo = obj.foo)(); // =>2
```

<!--more-->

分析：
1.`obj.foo()` 这里是 `obj` 调用 `foo()` 方法，所以 `foo()` 方法中的 `this` 指向的 `obj`
2.`other_foo()` 这里的 `this` 指向的是 `window`
3.`(p.foo = obj.foo)()` 首先 `p.foo = obj.foo` 返回的是 `foo` 函数，再调用 `foo`, 这里的 `this` 指向的是 `window`

## 0x01
```js
foo();   // => 1
var foo;
function foo() {
  console.log(1);
}
console.log(typeof foo === 'function') // => true
foo = function() {
  console.log(2);
}
foo();   // => 2
```

分析：
1.变量提升，一开始 `foo()` 函数输出 `1`
2.给 `foo` 重新赋值，函数输出 `2`

## 0x02
```js
console.log(Object.getPrototypeOf(Object) === Function.prototype);             // true
console.log(Object.getPrototypeOf(Function.prototype) === Object.prototype);   // true
console.log(Object.getPrototypeOf(Object.prototype));                          // null
```

分析：
1.`Object.getPrototypeOf()` 方法返回指定对象的原型（内部 `[[Prototype]]` 属性的值）。`Object` 作为一个函数，它的原型是 `Function.prototype`
2.`Function.prototype` 取得是函数的原型，指向的是 `Object.prototype`
3.`Object.prototype` 的原型则是 `null`

JavaScript 中的 `Object` 是构造函数（创建对象的包装器）。
一般用法是：
```js
var obj = new Object();
```
所以：
```js
Object.getPrototypeOf( Object );               // ƒ () { [native code] }
Object.getPrototypeOf( Function );             // ƒ () { [native code] }

Object.getPrototypeOf( Object ) === Function.prototype;        // true
```

`Object.getPrototypeOf( Object )` 是把 Object 这一构造函数看作对象，
返回的当然是函数对象的原型，也就是 `Function.prototype`。

正确的方法是，`Object.prototype` 是构造出来的对象的原型。
```js
var obj = new Object();
Object.prototype === Object.getPrototypeOf( obj );              // true

Object.prototype === Object.getPrototypeOf( {} );               // true
```

4.神图
{% asset_img 1.jpg %}

## 0x03
```js
setTimeout(function () {
  console.log('1')
});
new Promise(function (resolve) {
  console.log('2');
  resolve();
  console.log('3');
}).then(function () {
  console.log('4')
});
console.log('5');
// 2
// 3
// 5
// 4
// 1
```

分析：
1.考察的是 Event Loop，`setTimeout` 放到宏任务中
2.`new Promise` 同步执行，输出 `2` `3`
3.将 `.then` 放到微任务中
4.执行同步任务 `console.log('5')`, 输出`5`
5.执行微任务`.then` 输出 `4`
6.执行宏任务，输出 `1`



