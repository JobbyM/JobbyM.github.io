---
title: 一日一练-JS for in 和 for of 的区别
date: 2021-07-09 17:11:24
tags:
  - 技术
  - 一日一练
  - JS
  - for...in
  - for...of
  - 迭代器
categories: 技术
---



## for...in
**for...in 语句**以任意顺序遍历一个对象的除 [Symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol) 以外的[可枚举](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Enumerability_and_ownership_of_properties)属性

### 语法
```js
for (variable in object)
    statement
```

<!--more-->

**variable**
    在每次迭代时，variable 会被赋值为不同的属性名。

**object**
    非 Symbol 类型的可枚举属性被迭代的对象。


### 数组迭代和 for...in
> 提示：`for...in` 不应该用于迭代一个关注索引顺序的 Array。

### 仅迭代自身的属性
如果你只要考虑对象本身的属性，而不是它的原型，那么使用 [getOwnPropertyNames()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames) 或执行 [hasOwnProperty()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty) 来确定某属性是否是对象本身的属性（也能使用 [propertyIsEnumerable](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/propertyIsEnumerable)）。或者，如果你知道不会有任何外部代码干扰，您可以使用检查方法扩展内置原型。

### 为什么用 for...in?
`for ... in` 是为遍历对象属性而构建的，不建议与数组一起使用，数组可以用 `Array.prototype.forEach()` 和 `for...of`，那么 `for...in` 的到底有什么用呢？

它最常用的地方应该是用于调试，可以更方便的去检查对象属性（通过输出到控制台或其他方式）。尽管对于处理存储数据，数组更实用些，但是你在处理有 `key-value` 数据（比如属性用作“键”），需要检查其中的任何键是否为某值的情况时，还是推荐用 `for...in`。

### 什么是对象中的常规属性和排序属性
```js
function Foo() {
    this[100] = 'test-100'
    this[1] = 'test-1'
    this["B"] = 'bar-B'
    this[50] = 'test-50'
    this[9] = 'test-9'
    this[8] = 'test-8'
    this[3] = 'test-3'
    this[5] = 'test-5'
    this["A"] = 'bar-A'
    this["C"] = 'bar-C'
}
var bar = new Foo()
for (key in bar) {
    console.log(`index:${key} value:${bar[key]}`)
}
```

在上⾯这段代码中，我们利⽤构造函数 Foo 创建了⼀个 bar 对象，在构造函数中，我们给bar对象设置了很多属性，包括了数字属性和字符串属性，然后我们枚举出来了 bar 对象中所有的属性，并将其⼀⼀打印出来， 下⾯就是执⾏这段代码所打印出来的结果
```bash
index:1 value:test-1
index:3 value:test-3
index:5 value:test-5
index:8 value:test-8
index:9 value:test-9
index:50 value:test-50
index:100 value:test-100
index:B value:bar-B
index:A value:bar-A
index:C value:bar-C
```

观察这段打印出来的数据，我们发现打印出来的属性顺序并不是我们设置的顺序，我们设置属性的时候是乱序设置的，⽐如开始先设置 100，然后有设置了 1，但是输出的内容却⾮常规律，总的来说体现在以下两 点：

* 设置的数字属性被最先打印出来了，并且按照数字⼤⼩的顺序打印的；

* 设置的字符串属性依然是按照之前的设置顺序打印的，⽐如我们是按照 B、A、C 的顺序设置的，打印出来，依然是这个顺序。

之所以出现这样的结果，是因为在 ECMAScript 规范中定义了 **「数字属性应该按照索引值⼤⼩升序排列，字符串属性根据创建时的顺序升序排列。」**在这⾥我们把对象中的数字属性称为 **「排序属性」**，在V8中被称为 elements，字符串属性就被称为 **「常规属性」**， 在 V8 中被称为 properties。在 V8 内部，为了有效地提升存储和访问这两种属性的性能，分别使⽤了两个**线性数据**结构来分别保存排序属性和常规属性，具体结构如下图所⽰：
{% asset_img 1.jpg %}

在 elements 对象中，会按照顺序存放排序属性，properties 属性则指向了 properties 对象，在 properties 对象中，会按照创建时的顺序保存了常规属性。

**总结一句: for in 循环特别适合遍历对象。**

## for...of
**for...of 语句**在[可迭代对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols)（包括 [Array](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array)，[Map](https://developer.mozilla.org/zh-CN/docs/orphaned/Web/JavaScript/Reference/Global_Objects/Map)，[Set](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set)，[String](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String)，[TypedArray](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)，[arguments](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions_and_function_scope/arguments) 对象等等）上创建一个迭代循环，调用自定义迭代钩子，并为每个不同属性的值执行语句

### 语法
```js
for (variable of iterable) {
    statement
}
```

**variable**
    在每次迭代中，将不同属性的值分配给变量。

**iterable**
    被迭代枚举其属性的对象。

### 示例
#### 迭代 Array
```js
let iterable = [10, 20, 30]

for (let value of iterable) {
    value += 1
    console.log(value)
}
// 11
// 21
// 31
```

#### 迭代 String
```js
let iterable = 'JobbyM'

for (let value of iterable) {
    console.log(value)
}
// J
// o
// b
// b
// y
// M
```

#### 迭代 TypedArray
```js
let iterable = new Uint8Array([0x00, 0xff])

for (let value of iterable) {
    console.log(value)
}
// 0
// 255
```

#### 迭代 Map
```js
let iterable = new Map([["a", 3],["b", 4],["c", 5]])

for (let entry of iterable) {
    console.log(entry)
}
// ["a", 3]
// ["b", 4]
// ["c", 5]

for (let [key, value] of iterable) {
    console.log(key, value)
}
// a 3
// b 4
// c 5
```

#### 迭代 Set
```js
let iterable = new Set([1, 1, 2, 2, 3, 3])

for (let value of iterable) {
    console.log(value)
}
// 1
// 2
// 3
```

#### 迭代 arguments 对象
```js
(function(){
    for (let argument  of arguments) {
        console.log(argument)
    }
})(1, 2, 3)

// 1
// 2
// 3
```
#### 迭代 DOM 集合
迭代 DOM 元素集合，比如一个 NodeList 对象：面的例子演示给每一个 article 标签内的 p 标签添加一个 "read" 类。
```js
//注意：这只能在实现了NodeList.prototype[Symbol.iterator]的平台上运行
let articleParagraphs = document.querySelectorAll("article > p");

for (let paragraph of articleParagraphs) {
  paragraph.classList.add("read");
}
```

### 关闭迭代器
对于 `for...of` 的循环，可以由 `break`, `throw`， `continue`  或 `return` 终止。在这些情况下，迭代器关闭。
```js
function * foo() {
    yield 1
    yield 2
    yield 3
}

for (let o of foo()) {
    console.log(o)
    break; // close iterator, triggers return
}
```

### 迭代生成器
你还可以迭代一个[生成器](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/function*)
```js
function* fibonacci() { // 一个生成器函数
    let [prev, curr] = [0, 1]
    for(;;){ // while(true)
        [prev, curr] = [curr, prev + curr]
        yield curr
    }
}

for (let n  of fibonacci()) {
    console.log(n)
    // 当 n 大于 1000 时跳出循环
    if (n > 1000) 
        break;
}
```

### 不要重用生成器
生成器不应该重用，即使 `for...of` 循环的提前终止，例如通过 `break` 关键字。在退出循环后，生成器关闭，并尝试再次迭代，不会产生任何进一步的结果。
```js
var gen = (function *(){
    yield 1
    yield 2
    yield 3
})()
for (let o of gen) {
    console.log(o)
    break; // 关闭生成器
}

// 生成器不应该重用，以下没有意义
for (let o of gen) {
    console.log(o)
}
```

### 迭代其他可迭代对象
你还可以迭代显示实现可迭代协议的对象：
```js
var iterable = {
  [Symbol.iterator]() {
    return {
      i: 0,
      next() {
        if (this.i < 3) {
          return { value: this.i++, done: false };
        }
        return { value: undefined, done: true };
      }
    };
  }
};

for (var value of iterable) {
  console.log(value);
}
// 0
// 1
// 2
```

## `for...of` 与 `for...in` 的区别
无论是 `for...in` 还是 `for...of` 语句都是迭代一些东西。它们之间的主要区别在于它们的迭代方式。

`for...in` 语句以任意顺序迭代对象的**可枚举属性**。

`for...of` 语句遍历可迭代对象定义要**迭代的数据**。

以下示例显示了与 Array 一起使用时，`for...of` 循环和 `for...in` 循环之间的区别。
```js
Object.prototype.objCustom = function(){}
Array.prototype.arrCustom = function(){}

let iterable = [3, 5, 7]
iterable.foo = 'hello'

for (let i in iterable) {
    console.log(i) // 0, 1, 2, 'hello', 'arrCustom', `objCustom`
}

for (let i in iterable) {
    if (iterable.hasOwnProperty(i)) {
        console.log(i) // 0, 1, 2, 'hello'
    }
}
for (let i of iterable) {
    console.log(i) // 3, 5, 7
}
```

```js
Object.prototype.objCustom = function(){}
Array.prototype.arrCustom = function(){}

let iterable = [3, 5, 7]
iterable.foo = 'hello'
```

每个对象将继承 objCustom 属性，并且作为 Array 的每个对象将继承 arrCustom 属性，因为将这些属性添加到 `Object.prototype` 和 `Array.prototype`。由于继承和原型链，对象 iterable 继承属性 objCustom 和 arrCustom。

```js
for (let i in iterable) {
    console.log(i) // 0, 1, 2, 'hello', 'arrCustom', `objCustom`
}
```

此循环仅以原始插入顺序记录 iterable 对象的可枚举属性。它不记录数组**元素** 3, 5, 7 或hello，因为这些**不是**枚举属性。但是它记录了数组索引以及 arrCustom 和 objCustom。

```js
for (let i in iterable) {
    if (iterable.hasOwnProperty(i)) {
        console.log(i) // 0, 1, 2, 'hello'
    }
}
```

这个循环类似于第一个，但是它使用 hasOwnProperty() 来检查，如果找到的枚举属性是对象自己的（**不是继承的**）。如果是，该属性被记录。记录的属性是 0, 1, 2 和 foo，因为它们是自身的属性（**不是继承的**）。属性 arrCustom 和 objCustom 不会被记录，因为它们是**继承的**。

```js
for (let i of iterable) {
    console.log(i) // 3, 5, 7
}
```

该循环迭代并记录 iterable 作为可迭代对象定义的**迭代值**，这些是数组元素 3, 5, 7，**而不是任何对象的属性**。

## 参考文档
1. [MDN for...in](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...in)
2. [MDN for...of](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...of)
3. [百度前端面试题：for in 和 for of的区别详解以及为for in的输出顺序](https://zhuanlan.zhihu.com/p/161892289)