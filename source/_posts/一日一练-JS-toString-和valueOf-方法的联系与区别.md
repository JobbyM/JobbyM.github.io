---
title: 一日一练-JS toString 和valueOf 方法的联系与区别
date: 2018-04-23 15:39:40
tags:
---

> 子曰：类型转换中toString 和valueOf 的联系与区别分析

首先是看看ES5 的规范是如何进行说明的

在这里有几个基础知识点需要了解一下：
1. `[[Class]]`
`[[Class]]` 属于Object 的内部属性，值的类型返回为`String`，其作用是，说明规范定义的对象分类的一个字符串值。
ES5 规范的每种内置对象都定义了`[[Class]]` 内部属性值。宿主对象的`[[Class]]` 内部属性值可以是除了“Arguments”、 “Array”、 “Boolean”、 “Date”、 “Error”、 “Function”、 “JSON”、 “Math”、 “Number”、 “Object”、 “RegExp”、 “String” 的任意字符串。`[[Class]]` 内部属性的值用于内部区分对象的种类。注，本规范中除了通过`Object.prototype.toString` 没有提供任何手段使程序访问此值。
> 摘自[ES5 类型](https://www.w3.org/html/ig/zh/wiki/ES5/%E7%B1%BB%E5%9E%8B#Class)

2. `[[PrimitiveValue]]`
`[[PrimitiveValue]]` 属于只在某些对象中定义的内部属性，其值的类型范围为原始类型，作用是说明与此对象的内部状态信息关联。对于标准内置对象只能用 **Boolean、Date、Number、String 对象实现** `[[PrimitiveValue]]`。
> 摘自[ES5 类型](https://www.w3.org/html/ig/zh/wiki/ES5/%E7%B1%BB%E5%9E%8B#Object.E7.9A.84.E5.86.85.E9.83.A8.E5.B1.9E.E6.80.A7.E5.8F.8A.E6.96.B9.E6.B3.95)

3. ToObject
`ToObject` 抽象操作根据下表将其参数转换为对象类型的值：

| 输入类型 | 结果 |
| :- | :- |
| 未定义 | 抛出TypeError 异常。 |
| 空值 | 抛出TypeError 异常。 |
| 布尔值 | 创建一个新的Boolean 对象，其`[[PrimitiveValue]]` 属性被设为该参数的值。 |
| 数值 | 创建一个新的Number 对象，其`[[PrimitiveValue]]` 属性被设为该参数的值。 |
| 字符串 | 创建一个新的String 对象，其`[[PrimitiveValue]]` 属性被设为该参数的值。 |
| 对象 | 结果是输入的参数（不转换） |

> 摘自[ES5 类型转换与测试](https://www.w3.org/html/ig/zh/wiki/ES5/%E7%B1%BB%E5%9E%8B%E8%BD%AC%E6%8D%A2%E4%B8%8E%E6%B5%8B%E8%AF%95#ToObject)

## Object.prototype.toString()
当调用`toString` 方法，采用如下步骤：
 1. 如果`this` 的值是`undefined`, 返回 **[object Undefined]**。
 2. 如果`this` 的值是`null`, 返回 **[object Null]**。
 3. 令O 为以`this` 作为参数调用`ToObject` 的结果。
 4. 令`class` 为O 的`[[Class]]` 内部属性的值。
 5. 返回三个字符串"[Object "、 `class` 和"]" 连起来的字符串。
> 摘自[ES5 Object.prototype.toString() ](https://www.w3.org/html/ig/zh/wiki/ES5/%E6%A0%87%E5%87%86_ECMAScript_%E5%86%85%E7%BD%AE%E5%AF%B9%E8%B1%A1#Object.prototype.toString_.28_.29)


## Object.prototype.valueOf()
当调用`valueOf` 方法，采用如下步骤：
 1. 令O 为以`this` 作为参数调用`ToObject` 的结果。
 2. 如果O 是以宿主对象[15.2.2.1](https://www.w3.org/html/ig/zh/wiki/ES5/%E6%A0%87%E5%87%86_ECMAScript_%E5%86%85%E7%BD%AE%E5%AF%B9%E8%B1%A1#15.2.2.1)作为参数调用`Object` 构造器的结果，则
  1. 返回O 或返回先前传递给构造器的原宿主对象。返回的具体结果是由实现定义的。
 3. 返回O。
> 摘自[ES5 Object.prototype.valueOf()](https://www.w3.org/html/ig/zh/wiki/ES5/%E6%A0%87%E5%87%86_ECMAScript_%E5%86%85%E7%BD%AE%E5%AF%B9%E8%B1%A1#Object.prototype.valueOf_.28_.29)


## toString() 和valueOf()
所有对象继承了两个转换方法。
1. `toString()`
  作用是返回一个反映这个对象的字符串。默认的`toString()` 方法返回值
  ```js
  ({x:1, y:2}).toString() // => "[object Object]"
  ```
  1. 数组类（Array class）
    将每个数组元素转换为一个字符串，并在元素之间添加逗号后合并结果字符串。
    ```js
      [1,2,3].toString() // => "1,2,3"
    ```
  2. 函数类（Function class）
    返回这个函数的实现定义的表示方法。实际上，这里的实现方式是通常是将用户定义的函数转换为JavaScript 源代码字符串。
    ```js
      (function(x) { f(x) }).toString()  // => "function(x) {\n f(x) \n }"
    ```
  3. 日期类（Date class）
    返回一个可读的（意指可以通过JavaScript 的方法过了并再做封装）日期和时间字符串。
    ```js
    new Date().toString() // => "Tue Apr 24 2018 09:43:31 GMT+0800 (中国标准时间)"
    ```
  4. RegExp 类（RegExp class）
    将RegExp 对象转换为表示正则表达式直接量的字符串。
    ```js
    /\d+/g.toString() // => "/\\d+/g"
    ```
2. valueOf()
  这个方法的任务并未详细定义：如果存在任意原始值，它就默认将对象转换为表示它的原始值。对象是复合值，而且诶额大多数对象无法真正表示为一个原始值，因此默认的`valueOf()` 方法简单地返回对象本身，而不是返回一个原始值。
  1. 数组、函数和正则表达式简单地继承了这个默认方法，调用这些类型的实例的`valueOf()` 方法只是简单返回对象本身。
  2. 日期类定义的`valueOf()` 方法会返回它额一个内部表示：1970年1月1日以来的毫秒数。
  ```js
  var d = new Date(2018,4,1) // => 2018年5月1日
  d.valueOf()                // => 1525104000000
  ```

## 对象转换为原始值
1. 对象到布尔值的转换：
  所有对象（包括数值和函数）都转换为`true`。对于包装函数亦是如此：`new Boolean(false)` 是一个对象而不是原始值，它将转化为`true`
2. 对象到字符串的转换：
  1. 如果对象具有`toString()` 方法，则调用这个方法。如果它返回一个原始值，JavaScript 将原始值转换为字符串（如果它本身不是字符串的话），并返回这个字符串的结果。
  2. 如果对象没有`toString()` 方法， 或者这个方法并不返回一个原始值，那么JavaScript 会调用`valueOf()` 方法。如果存在这个方法，则JavaScript 调用它。如果返回值是原始值，JavaScript 将这个值转换为字符串（如果本身不是字符串的话），并返回这个字符串的结果。
  3. 否则，JavaScript 无法从`toString()` 或`valueOf()` 获得一个原始值，因此这时它将抛出一个类型错误异常。
3. 对象到数字的转换：
  1. 如果对象具有`valueOf()` 方法，后者返回一个原始值，则JavaScript 将这个原始值转换为数字（如果需要的话）并返回这个数字。
  2. 否则，如果对象具有`toString()` 方法，后者返回一个原始值，则JavaScript 将其转换并返回。对象的`toString()` 方法返回一个字符串直接量（这里的原始值），JavaScript 将这个字符串转换为数字类型，并返回这数字。
  3. 否则，JavaScript 抛出一个类型错误异常。

## 实例讲解
1. 空数组会被转换为数字0，具有单个元素的数组同样会转换为一个数组。
```js
Number([]) // => 0
Number(['1234']) // => 1234
```

数组继承了默认的`valueOf()` 方法，这个方法返回一个对象而不是一个原始值，因此，数组到数字的转换则调用`toString()` 方法。空数组转换为空字符串，空字符串转换成数字0.
```js
// [] => '' => 0
[].valueOf() // => []
[].toString() // => ''
'' // => 0
```
含有一个元素的数组转换为字符串的结果和这个元素转换字符串的结果一样。如果数组只包含一个数字元素，这个数字转换为字符串，再转换会数字。
```js
[1].valueOf() // => [1]
[1].toString() // => "1"
"1" // => 1
```
2. `+` 运算符可以进行数字加法和字符串连接操作。
如果它的其中一个操作数是对象，则JavaScript 将使用特殊的方法将对象转换为原始值，而不是执行对象到方法的转换。
这种特殊方式是指：通过`valueOf()` 或者`toString()` 返回的原始值将被直接使用，而不会被强制转换为数字或字符串。
`+`、`==`、`!=` 和关系运算符也会做对象到原始值的转换，但要除去日期对象的特殊情形：任何对象都会首先尝试调用valueOf()，然后调用toString()。不管得到的原始值是否直接使用，它都不会进一步被转换为数字或字符串。 
