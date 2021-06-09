---
title: 一日一练-JS 实现继承
date: 2021-06-04 18:01:09
tags:
  - 技术
  - 继承
categories: 技术
---

> 子曰：万里之行，始于足下。

## 借用构造函数
这种技术的基本思想很简单，就是在子类型构造函数的内部调用超类型的构造函数。另外，函数只不过是在特定环境中执行代码的对象，因此通过使用 apply() 和 call() 方法也可以在新创建的对象上执行构造函数。

<!--more-->

```js
function Box(name) {
  this.name = name 
}
Box.prototype.age = 18

function Desk(name) {
  Box.call(this, name) // 对象冒充，对象冒充只能继承构造函数里的信息
}

var desk = new Desk('jobbym')
console.log(desk.name)  // -> jobbym
console.log(desk.age)   // -> undefined
```

从中可以看到，继承来的只有实例属性，而原型上的属性是访问不到的。这种模式解决了两个问题，就是可以传参，可以继承，但是没有原型，就没有办法复用。

## 组合继承
```js
  function Box(name) {
    this.name = name
  }
  Box.prototype.age = 18
  Box.prototype.run = function () {
    console.log(this.name + '正在运行')
  }

  function Desk(name) {
    Box.call(this, name) // 对象冒充，对象冒充只能继承构造函数里的信息
  }
  Desk.prototype = new Box() // 原型链
  var desk = new Desk('jobbym')
  console.log(desk.name)  // -> jobbym
  console.log(desk.age)   // -> 18
  desk.run()              // jobbym正在运行
  console.log(desk)
```

{% asset_img 1.png %}

这种继承方式的思路是：使用原型链的方式来实现对原型属性和方法的继承，通过借用构造函数来实现对实例属性的继承。

## 原型式继承
原型式继承：是借助原型可以基于已有的对象创建新对象，同时还不必因此创建自定义类型。讲到这里必须得提一个人，道格拉斯.克罗克福德在 2006 写的一篇文章《Prototype inheritance in Javascript》（Javascript 中的原型式继承）中给出了一个方法：
```js
  function object(o) { // 传递一个字面量对象
    function F(){}  // 创建一个构造函数
    F.prototype = o // 把字面量对象赋值给构造函数的原型
    return new F()  // 最终返回实例化的构造函数
  }
```

看下面的例子：
```js
  function obj(o) { // 传递一个字面量对象
    function F(){}  // 创建一个构造函数
    F.prototype = o // 把字面量对象赋值给构造函数的原型
    return new F()  // 最终返回实例化的构造函数
  }

  var box = {
    name: 'jobbym',
    age: 18,
    family: ['哥哥', '姐姐']
  }

  var box1 = obj(box)
  console.log(box1)
  console.log(box1.name)    // -> jobbym
  box1.family.push('妹妹') 
  console.log(box1.family)  // -> ['哥哥', '姐姐', '妹妹']

  var box2 = obj(box)
  console.log(box2.family)  // -> ['哥哥', '姐姐', '妹妹']
```

{% asset_img 2.png %}

因为上述的代码的实现逻辑跟原型链继承很相似，所以里面的引用数组，即 family 属性被共享了。

## 寄生式继承
```js
  function obj(o) { // 传递一个字面量对象
    function F() { }  // 创建一个构造函数
    F.prototype = o // 把字面量对象赋值给构造函数的原型
    return new F()  // 最终返回实例化的构造函数
  }
  function create(o) {
    var clone = obj(o)            // 通过调用函数创建一个新对象
    clone.sayName = function() {  // 以某种方式来增强这个对象
      console.log('hi')
    }
    return clone                   // 返回这个对象
  }

  var person = {
    name: 'jobbym',
    friends: ['tom', 'jerry']
  }

  var anotherPerson = create(person)
  anotherPerson.sayName()  // -> hi
  anotherPerson.name = 'luccy'
  console.log(anotherPerson.name)     // -> luccy
  console.log(anotherPerson.friends)  // -> ['tom', 'jerry']
  console.log(anotherPerson)
```

{% asset_img 3.png %}

这个例子中的代码基于 person 返回一个新对象 -- anotherPerson。新对象不仅具有 person 的所有属性和方法，而且还有自己的 sayHi() 方法。在主要考虑对象而不是自定义类型和构造函数的情况下，寄生式继承也是一种有用的模式。使用寄生式继承为对象添加函数，会由于不能做到函数复用而降低效率，这一点与构造函数模式类似。

## 寄生组合式继承
前面说过，组合继承是 JavaScript 最常用的继承模式，不过，他也有自己的不足。组合继承最大的问题在于无论什么情况下，都会调用两次超类型构造函数：一次是在创建子类型原型的时候，另一次是在子类型构造函数内部。没错，子类型最终会包含超类型对象的全部实例属性，但我们不得不在调用子类型构造函数时重写这些属性，再来看一看下面的例子。
```js
  function SuperType(name) {
    this.name = name
    this.colors = ['red', 'black']
  }
  SuperType.prototype.sayName = function () {
    console.log(this.name)
  }

  function SubType(name, age) {
    SuperType.call(this, name)   // 第二次调用 SuperType
    this.age = age
  }
  SubType.prototype = new SuperType() // 第一次调用 SuperType
  SubType.prototype.constructor = SubType
  SubType.prototype.sayAge = function () {
    console.log(this.age)
  }

  var sub = new SubType('jobbym', 18) 
  sub.sayName()                    // -> jobbym
  sub.sayAge()                     // -> 18
  console.log(sub.colors)          // ['red', 'black']
  console.log(sub instanceof SubType)  // -> true
  console.log(sub instanceof SuperType)// -> true
  console.log(sub)
```

{% asset_img 4.png %}

第一次调用 SuperType 构造函数时，SubType.prototype 会得到两个属性：name 和 colors。他们都是 SuperType 的实例属性，只不过现在位于 SubType 的原型中。当调用 SubType 构造函数时，又会调用一次 SuperType 构造函数，这一次又在新对象上创建了实例属性 name 和 colors。于是，这两个属性就屏蔽了原型中的两个同名属性。即有两组 name 和 colors 属性：一组在实例上，一组在原型上。这就调用两次 SuperType 构造函数的结果。解决这个问题的方法就是 -- 寄生组合式继承。

所谓寄生组合式继承，即通过借用构造函数来继承属性，通过原型链的混成形式来继承方法。其背后的基本思路是：不必为了指定子类型的原型而调用超类型的构造函数，我们所需要的无非就是超类型原型的一个副本而已。本质上，就是使用寄生式继承来继承超类型的原型，然后再将结果指定给子类型的原型。寄生组合式继承的基本模式如下：
```js
function object(o) {
  function F(){}
  F.prototype = o
  return new F()
}

function inheritPrototype(subType, superType) {
  var prototype = object(superType.prototype)   // 创建对象
  prototype.constructor = subType               // 增强对象
  subType.prototype = prototype                 // 指定对象
}

function SuperType(name) {
  this.name = name
  this.colors = ['red', 'black']
}
SuperType.prototype.sayName = function () {
  console.log(this.name)
}

function SubType(name, age) {
  SuperType.call(this, name)   // 第二次调用 SuperType
  this.age = age
}
// SubType.prototype = new SuperType() // 第一次调用 SuperType
// SubType.prototype.constructor = SubType

inheritPrototype(SubType, SuperType)

SubType.prototype.sayAge = function () {
  console.log(this.age)
}

var sub = new SubType('jobbym', 18)
sub.sayName()                    // -> jobbym
sub.sayAge()                     // -> 18
console.log(sub.colors)          // ['red', 'black']
console.log(sub instanceof SubType)  // -> true
console.log(sub instanceof SuperType)// -> true
console.log(sub)
```

控制台打印出的结构：
{% asset_img 5.png %}

详细的图解：
{% asset_img 6.png %}

这个例子的高效率体现在他只调用了一次 SuperType 构造函数，并且因此避免了在 SubType.prototype 上面创建不必要的、多余的属性。与此同时，原型链还能保持不变；因此，还能够正常使用 instanceof 和 isPrototypeOf()。

## 参考文档
1.[js中实现继承的五种方法](https://www.jb51.net/article/204744.htm)
1.[js中继承的6种方式详解](https://www.jb51.net/article/208954.htm)
