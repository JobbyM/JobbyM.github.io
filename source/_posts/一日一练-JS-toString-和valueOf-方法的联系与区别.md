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
 2. 如果O 是以宿主对象[15.2.2.1]()作为参数调用`Object` 构造器的结果，则
  1. 返回O 或返回先前传递给构造器的原宿主对象。返回的具体结果是由实现定义的。
 3. 返回O。
> 摘自[ES5 Object.prototype.valueOf()](https://www.w3.org/html/ig/zh/wiki/ES5/%E6%A0%87%E5%87%86_ECMAScript_%E5%86%85%E7%BD%AE%E5%AF%B9%E8%B1%A1#Object.prototype.valueOf_.28_.29)
