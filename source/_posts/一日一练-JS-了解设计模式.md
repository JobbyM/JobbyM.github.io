---
title: 一日一练-JS 了解设计模式
date: 2021-06-10 09:26:27
tags:
  - 技术
  - 设计模式
  - 转载
categories: 技术
---

> 转自的 w3cshool.cn [JavaScript 设计模式](https://www.w3cschool.cn/zobyhd/3lt2rcqm.html)，有删节。 


在本节中，我们将探讨一些经典和现代的设计模式的 JavaScript 实现。

开发人员通常想知道在他们的工作流程中是否有一个（或一组）理想的模式。这个问题没有一个真正的单一答案：我们要完成的每个脚本和 Web 应用都可能会有它自己的独特需求，我们需要思考模式对实现来说在哪些方面能够提供真正的价值。

例如，有些项目可能很需要观察者模式提供的解耦（降低应用中各部分的彼此依赖）好处，但对其它根本不关切解耦的项目来说几乎用不上这个模式的好处。

换言之，一旦我们对设计模式和具体问题的最适合匹配有坚定的把握，就更容易把它们融入我们的应用程序架构中。

<!--more-->


## JavaScript 构造器模式

### 构造器模式
在面向对象编程中，构造器是一个当新建对象的内存被分配后，用来初始化该对象的一个特殊函数。在 JavaScript 中几乎所有的东西都是对象，我们经常会对对象的构造器十分感兴趣。

对象构造器是被用来创建特殊类型的对象的，首先它要准备使用的对象，其次在对象初次被创建时，通过接收参数，构造器要用来对成员的属性和方法进行赋值。

### 对象创建
下面是我们创建对象的三种基本方式:

下面的每一种都会创建一个新的对象:
```js
var newObject = {}

// or
var newObject = Object.create(null)

//or
var newObject = new Object()
```

最后一个例子中 “Object” 构造器创建了一个针对特殊值的对象包装，只不过这里没有传值给他，所以他会返回一个空对象。
```js
// ECMAScript 3 兼容形式

// 1\. “点号”法

// 设置属性
newObject.someKey = "Hello World";

// 获取属性
var key = newObject.someKey;

// 2\. “方括号”法

// 设置属性
newObject["someKey"] = "Hello World";

// 获取属性
var key = newObject["someKey"];

// ECMAScript 5 仅兼容性形式
// For more information see: http://kangax.github.com/es5-compat-table/

// 3\. Object.defineProperty方式

// 设置属性
Object.defineProperty(newObject, 'someKey', {
    value: "for more control of the property's behavior",
    writable: true,
    enumerable: true,
    configurable: true
})

// 如果上面的方式你感到难以阅读，可以简短的写成下面这样：
var defineProp = function(obj, key, value) {
    config.value = value
    Object.defineProperty(obj, key, config)
}

// 为了使用他，我们要创建一个 “person” 对象
var person = Object.create(null)

// 用属性构造对象
defineProp(person, "car", "Delorean")
defineProp(person, "dateOfBirth", "1981")
defineProp(person, "hasBeard", false)


// 4\. Object.defineProperties方式

// 设置属性
Object.defineProperties(newObject, {

  "someKey": { 
    value: "Hello World", 
    writable: true 
  },

  "anotherKey": { 
    value: "Foo bar", 
    writable: false 
  } 

})

// 3和4中的读取属行可用1和2中的任意一种
```

在这本书的后面一点，这些方法会被用于继承，如下：
```js
// 使用:

// 创建一个继承与Person的赛车司机
var driver = Object.create( person );

// 设置司机的属性
defineProp(driver, "topSpeed", "100mph");

// 获取继承的属性 (1981)
console.log( driver.dateOfBirth );

// 获取我们设置的属性 (100mph)
console.log( driver.topSpeed );
```

### 基础构造器
正如我们先前所看到的，Javascript 不支持类的概念，但它有一种与对象一起工作的构造器函数。使用 new 关键字来调用该函数，我们可以告诉 Javascript 把这个函数当做一个构造器来用,它可以用自己所定义的成员来初始化一个对象。

在这个构造器内部，关键字 this 引用到刚被创建的对象。回到对象创建，一个基本的构造函数看起来像这样:
```js
function Car(model, year, miles) {

    this.model = model
    this.year = year
    this.miles = miles

    this.toString = function () {
        return this.model + " has done " + this.miles + " miles"
    }
}

// 使用：

// 我们可以实例化一个 Car
var civic = new Car('Honda Civic', 2009, 20000)
var mondeo = new Car('Ford Mondeo', 2010, 5000)

// 打开浏览器控制台查看这些对象toString()方法的输出值
console.log(civic.toString())
console.log(mondeo.toString())
```

上面这是个简单版本的构造器模式，但它还是有些问题。一个是难以继承，另一个是每个Car 构造函数创建的对象中，toString() 之类的函数都被重新定义。这不是非常好，理想的情况是所有 Car 类型的对象都应该引用同一个函数。 这要谢谢 ECMAScript3 和 ECMAScript5-兼容版，对于构造对象他们提供了另外一些选择，解决限制小菜一碟。

### 使用“原型”的构造器
在 Javascript 中函数有一个 prototype 的属性。当我们调用 Javascript 的构造器创建一个对象时，构造函数 prototype 上的属性对于所创建的对象来说都看见。照这样，就可以创建多个访问相同 prototype 的 Car 对象了。下面，我们来扩展一下原来的例子：
```js
function Car(model, year, miles) {

    this.model = model
    this.year = year
    this.miles = miles

}

// 注意这里我们使用 Object.prototype.newMethod 而不是
// Object.prototype ，以避免我们重新定义原型对象
Car.prototype.toString = function () {
  return this.model + " has done " + this.miles + " miles"
}

// 使用：

// 我们可以实例化一个 Car
var civic = new Car('Honda Civic', 2009, 20000)
var mondeo = new Car('Ford Mondeo', 2010, 5000)

// 打开浏览器控制台查看这些对象toString()方法的输出值
console.log(civic.toString())
console.log(mondeo.toString())
```

通过上面代码，单个toString()实例被所有的Car对象所共享了。

## JavaScript 模块化模式
### 模块
模块是任何健壮的应用程序体系结构不可或缺的一部分，特点是有助于保持应用项目的代码单元既能清晰地分离又有组织。

在 JavaScript 中，实现模块有几个选项，他们包括：

1. 模块化模式
2. 对象表示法
3. AMD模块
4. CommonJS 模块
5. ECMAScript Harmony 模块

我们在书中后面的现代模块化JavaScript设计模式章节中将探讨这些选项中的最后三个。

模块化模式是基于对象的文字部分，所以首先对于更新我们对它们的知识是很有意义的。

### 对象字面值
在对象字面值的标记里，一个对象被描述为一组以逗号分隔的名称/值对括在大括号（{}）的集合。对象内部的名称可以是字符串或是标记符后跟着一个冒号":"。在对象里最后一个名称/值对不应该以","为结束符，因为这样会导致错误。
```js
var myObjectLiteral = {

    variableKey: variableValue,

    functionKey: function () {

    }
}
```

对象字面值不要求使用新的操作实例，但是不能够在结构体开始使用，因为"{"可能被解释为一个块的开始。在对象外新的成员会被加载，使用分配如下：`smyModule.property = "someValue";` 下面我们可以看到一个更完整的使用对象字面值定义一个模块的例子：
```js
var myModule = {

	myProperty: 'someValue',

	// 对象字面值包含了属性和方法
	// 例如，我们可以定义一个模块配置对象：
	myConfig: {
		useCaching: true,
		language: 'en'
	},

	// 非常基本的方法
	myMethod: function () {
		console.log('Where in the world is Paul Irish today?')
	},

	// 输出当前配置
	myMethod2: function () {
		console.log('Caching is: ' + (this.myConfig.useCaching ? 'enable' : 'disable'))
	},

	// 重写当前配置
	myMethod3: function (newConfig) {
		if (typeof newConfig === 'object') {
			this.myConfig = newConfig
			console.log(this.myConfig.language)
		}
	}
}

myModule.myMethod()
// 输出：Where in the world is Paul Irish today?

myModule.myMethod2()
// 输出：Caching is: enable

myModule.myMethod3({
	language: 'fr',
	useCaching: false
})
// 输出： fr
```

使用对象字面值可以协助封装和组织你的代码。如果你想近一步了解对象字面值可以阅读 [Rebecca Murphey 写过的关于此类话题的更深入的文章](http://rmurphey.com/blog/2009/10/15/using-objects-to-organize-your-code/)。

也就是说，如果我们选择了这种技术，我们可能对模块模式有同样的兴趣。即使使用对象字面值，但也只有一个函数的返回值。

### 模块化模式
模块化模式最初被定义为一种对传统软件工程中的类提供私有和公共封装的方法。

在 JavaScript 中，模块化模式用来进一步模拟类的概念，通过这样一种方式：我们可以在一个单一的对象中包含公共/私有的方法和变量，从而从全局范围中屏蔽特定的部分。这个结果是可以减少我们的函数名称与在页面中其他脚本区域定义的函数名称冲突的可能性。

#### 私有信息
模块模式使用闭包的方式来将"私有信息",状态和组织结构封装起来。提供了一种将公有和私有方法，变量封装混合在一起的方式，这种方式防止内部信息泄露到全局中，从而避免了和其它开发者接口发生冲图的可能性。在这种模式下只有公有的API 会返回，其它将全部保留在闭包的私有空间中。

这种方法提供了一个比较清晰的解决方案，在只暴露一个接口供其它部分使用的情况下，将执行繁重任务的逻辑保护起来。这个模式非常类似于立即调用函数式表达式(IIFE)，但是这种模式返回的是对象，而立即调用函数表达式返回的是一个函数。

需要注意的是，在 javascript 事实上没有一个显式的真正意义上的"私有性"概念，因为与传统语言不同，javascript 没有访问修饰符。从技术上讲，变量不能被声明为公有的或者私有的，因此我们使用函数域的方式去模拟这个概念。在模块模式中，因为闭包的缘故，声明的变量或者方法只在模块内部有效。在返回对象中定义的变量或者方法可以供任何人使用。

#### 历史
从历史角度来看，模块模式最初是在2003年由一群人共同发展出来的，这其中包括 Richard Cornford。后来通过 Douglas Crockford 的演讲，逐渐变得流行起来。另外一件事情是，如果你曾经用过雅虎的 YUI 库，你会看到其中的一些特性和模块模式非常类似，而这种情况的原因是在创建 YUI 框架的时候，模块模式极大的影响了 YUI 的设计。

#### 例子
下面这个例子通过创建一个自包含的模块实现了模块模式。
```js
var testModule = (function () {
	var counter = 0

	return {
		incrementCounter: function () {
			return counter ++
		},

		resetCounter: function () {
			console.log('counter value prior to reset: ' + counter)
			counter = 0
		}
	}
})()

// 用例

// 将 counter + 1
testModule.incrementCounter()

// 检测 counter 值，并重新为 0
testModule.resetCounter()
// -> 1
```

在这里我们看到，其它部分的代码不能直接访问我们的 incrementCounter() 或者  resetCounter() 的值。counter 变量被完全从全局域中隔离起来了，因此其表现的就像一个私有变量一样，它的存在只局限于模块的闭包内部，因此只有两个函数可以访问 counter。我们的方法是有名字空间限制的，因此在我们代码的测试部分，我们需要给所有函数调用前面加上模块的名字(例如"testModule")。

当使用模块模式时，我们会发现通过使用简单的模板，对于开始使用模块模式非常有用。下面是一个模板包含了命名空间，公共变量和私有变量
```js
var myNamespace = (function () {
	var myPrivateVar, myPrivateMethod;

	// 私有计数变量
	myPrivateVar = 0

	// 私有方法用来打印参数
	myPrivateMethod = function (foo) {
		console.log(foo)
	}

	return {
		// 公共变量
		myPublicVar: 'foo',

		// 公共方法里使用了私有变量和私有方法
		myPublicFunction: function (bar) {
			// 私有计数变量 + 1
			myPrivateVar ++

			// 调用私有方法
			myPrivateMethod(bar)
		}
	}
})()
```

### 优势
既然我们已经看到单例模式很有用，为什么还是使用模块模式呢？首先，对于有面向对象背景的开发者来讲，至少从 javascript 语言上来讲，模块模式相对于真正的封装概念更清晰。

其次，模块模式支持私有数据-因此，在模块模式中，公共部分代码可以访问私有数据，但是在模块外部，不能访问类的私有部分（没开玩笑！感谢David Engfer 的玩笑）。

### 缺点
模块模式的缺点是因为我们采用不同的方式访问公有和私有成员，因此当我们想要改变这些成员的可见性的时候，我们不得不在所有使用这些成员的地方修改代码。

我们也不能在对象之后添加的方法里面访问这些私有变量。也就是说，很多情况下，模块模式很有用，并且当使用正确的时候，潜在地可以改善我们代码的结构。

其它缺点包括不能为私有成员创建自动化的单元测试，以及在紧急修复bug时所带来的额外的复杂性。根本没有可能可以对私有成员打补丁。相反地，我们必须覆盖所有的使用存在bug私有成员的公共方法。开发者不能简单的扩展私有成员，因此我们需要记得，私有成员并非它们表面上看上去那么具有扩展性。

想要了解更深入的信息，可以阅读[ Ben Cherry 这篇精彩的文章](http://www.adequatelygood.com/JavaScript-Module-Pattern-In-Depth.html)。

## JavaScript 暴露模块模式
### 暴露模块模式
既然我们对模块模式已经有一些了解了，让我们看一下改进版本 - Christian Heilmann 的启发式模块模式。 启发式模块模式来自于，当 Heilmann 对这样一个现状的不满，即当我们想要在一个公有方法中调用另外一个公有方法，或者访问公有变量的时候，我们不得不重复主对象的名称。他也不喜欢模块模式中，当想要将某个成员变成公共成员时，修改文字标记的做法。

因此他工作的结果就是一个更新的模式，在这个模式中，我们可以简单地在私有域中定义我们所有的函数和变量，并且返回一个匿名对象，这个对象包含有一些指针，这些指针指向我们想要暴露出来的私有成员，使这些私有成员公有化。

下面给出一个如何使用暴露式模块模式的例子:
```js
var myRevealingModule = (function () {
	var privateVar = 'Ben Cherry',
		publicVar = 'Hey there!';
	
	function privateFunciton () {
		console.log('Name:' + privateVar)
	}

	function publicSetName(strName) {
		privateVar = strName
	}

	function publicGetName() {
		privateFunciton()
	}

	// Reveal public pointers to
	// private functions and properties

	return {
		setName: publicSetName,
		greeting: publicVar,
		getName: publicGetName
	}
})()

myRevealingModule.setName('Paul Kinlan')
myRevealingModule.getName() // -> Name:Paul Kinlan
```

这个模式可以用于将私有函数和属性以更加规范的命名方式展现出来。
```js
var myRevealingModule = (function () {
	var privateCounter = 0

	function privateFunction(){
		privateCounter ++
	}

	function publicFunction(){
		publicIncrement()
	}

	function publicIncrement(){
		privateFunction()
	}

	function publicGetCount(){
		return privateCounter
	}

	// Reveal public pointers to
	// private functions and properties
	
	return {
		start: publicFunction,
		increment: publicIncrement,
		count: publicGetCount
	}

})()

myRevealingModule.start()
myRevealingModule.count() // -> 1
```

### 优势
这个模式是我们脚本的语法更加一致。同样在模块的最后关于那些函数和变量可以被公共访问也变得更加清晰，增强了可读性。

### 缺点
这个模式的一个缺点是如果私有函数需要使用公有函数，那么这个公有函数在需要打补丁的时候就不能被重载。因为私有函数仍然使用的是私有的实现，并且这个模式不能用于公有成员，只用于函数。

公有成员使用私有成员也遵循上面不能打补丁的规则。

因为上面的原因，使用暴露式模块模式创建的模块相对于原始的模块模式更容易出问题，因此在使用的时候需要小心。

## JavaScript 单例模式
单例模式也称为单体模式，规定一个类只有一个实例，并且提供可全局访问点；

　　在读这篇文章之前，也许你对单例模式的概念感到模糊或者不清楚，但是其实在日常的开发中你肯定用到过单例模式；

　　JavaScript 中没有类的定义，单例模式的特点是”唯一“和”全局访问“，那么我们可以联想到 JavaScript 中的全局对象，利用 ES6 的 let 不允许重复声明的特性，刚好符合这两个特点；是的，全局对象是最简单的单例模式；
```js
let obj = {
    name: 'W3Cschool',
    getName: function(){}
}
```

上述代码中可以知道 obj 就是一个单例，因为 obj 刚好就符合单例模式的两大特点："唯一"和"可全局访问"；

但是我们并不建议这么实现单例，因为全局对象/全局变量会有一些弊端：

1. 污染命名空间（容易变量名冲突）
2. 维护时不容易管控 (搞不好就直接覆盖了)

### 简单版单例模式
  分析：只能有一个实例，所以我们需要使用 if 分支来判断，如果已经存在就直接返回，如果不存在就新建一个实例；
```js
let Singleton = function (name) {
	this.name = name
	this.instance = null
} 

Singleton.prototype.getName = function () {
	console.log(this.name)
}

Singleton.getInstance = function (name) {
	if (this.instance) {
		return this.instance
	}
	return this.instance = new Singleton(name)
}

let winner = Singleton.getInstance('winner')
console.log(winner.getName()) // -> winner
let sunner = Singleton.getInstance('sunner')
console.log(sunner.getName()) // -> winner
```

上面代码中我们是通过一个变量 instance 的值来进行判断是否已存在实例，如果存在就直接返回 this.instance，如果不存在，就新建实例并赋值给 instance；

　　但是上面的代码还是存在问题，因为创建对象的操作和判断实例的操作耦合在一起，并不符合”单一职责原则“；

### 改良版单例模式
    思路：通过一个闭包，来实现判断实例的操作；
```js
let CreateSingleton = (function () {
	let instance = null
	return function(name) {
		this.name = name
		if (instance) {
			return instance
		}
		return instance = this
	}
})()

CreateSingleton.prototype.getName = function () {
	console.log(this.name)
}

let winner = new CreateSingleton('winner')
console.log(winner.getName()) // -> winner
let sunner = new CreateSingleton('sunner')
console.log(sunner.getName()) // -> winner
```

### 代理版单例模式
    通过代理的形式，将创建对象的操作和实例判断的操作进行解耦拆分，实现更小粒度的划分，符合”单一职责原则“
```js
let ProxyCreateSingleton = (function () {
	let instance = null
	return function (name) {
		if (instance) {
			return instance
		}
		return instance = new Singlton(name)
	}
})()

let Singlton = function(name) {
	this.name = name
}

Singlton.prototype.getName = function () {
	console.log(this.name)
}

let winner = new ProxyCreateSingleton('winner')
console.log(winner.getName()) // -> winner
let sunner = new ProxyCreateSingleton('sunner')
console.log(sunner.getName()) // -> winner
```

上面的代码中，ProxyCreateSingleton() 只负责判断实例，Singlton 只负责创建对象和赋值；

### 惰性单例模式

　　 我们经常会有这样的场景：页面多次调用都有弹窗提示，只是提示内容不一样；

　　 这个时候我们可以立马想到是单例模式，弹窗就是单例实例，提示内容是参数传递；我们可以用惰性单例模式来实现它；
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>W3Cschool</title>
</head>
<body>
  <div id="loginBtn">W3Cschool</div>
</body>
<script>
  let getSingleton = function (fn) {
    var result
    return function () {
      return result || (result = fn.apply(this, arguments)) // 确定this上下文并传递参数
    }
  }

  let createAlertMessage = function (html) {
    var div = document.createElement('div')
    div.innerHTML = html
    div.style.display = 'none'
    document.body.appendChild(div)
    return div
  }

  let createSingleAlertMessage = getSingleton(createAlertMessage)
  document.getElementById('loginBtn').onclick = function () {
    let alertMessage = createSingleAlertMessage('<a href="https://www.w3cschool.cn/" style="text-decoration:none;" target="_blank">W3Cschool</a>')
    alertMessage.style.display = 'block'
  }
</script>
</html>
```
惰性单例是指的是页面开始加载的时候我们的实例是没有进行创建的，是当我们点击页面的 div 之后才开始创建实例（按需创建），这可以提高我们的网页性能，加快我们的页面渲染速度；


## JavaScript 观察者模式
观察者模式是这样一种设计模式。一个被称作被观察者的对象，维护一组被称为观察者的对象，这些对象依赖于被观察者，被观察者自动将自身的状态的任何变化通知给它们。

当一个被观察者需要将一些变化通知给观察者的时候，它将采用广播的方式，这条广播可能包含特定于这条通知的一些数据。

当特定的观察者不再需要接受来自于它所注册的被观察者的通知的时候，被观察者可以将其从所维护的组中删除。 在这里提及一下设计模式现有的定义很有必要。这个定义是与所使用的语言无关的。通过这个定义，最终我们可以更深层次地了解到设计模式如何使用以及其优势。在四人帮的《设计模式:可重用的面向对象软件的元素》这本书中，是这样定义观察者模式的:

一个或者更多的观察者对一个被观察者的状态感兴趣，将自身的这种兴趣通过附着自身的方式注册在被观察者身上。当被观察者发生变化，而这种便可也是观察者所关心的，就会产生一个通知，这个通知将会被送出去，最后将会调用每个观察者的更新方法。当观察者不在对被观察者的状态感兴趣的时候，它们只需要简单的将自身剥离即可。

我们现在可以通过实现一个观察者模式来进一步扩展我们刚才所学到的东西。这个实现包含一下组件:
* 被观察者：维护一组观察者，提供用于增加和移除观察者的方法。
* 观察者：提供一个更新接口，用于当被观察者状态变化时，得到通知。
* 具体的被观察者：状态变化时广播通知给观察者，保持具体的观察者的信息。
* 具体观察者：保持一个指向具体被观察者的引用，实现一个更新接口，用于观察，一边保证自身状态总是和被观察者状态一致的。

{% post_link 一日一练-JS-了解设计模式-观察者模式 一日一练-JS 了解设计模式-观察者模式 %}

观察者模式在应用设计中，解耦一系列不同的场景上非常有用，如果你没有用过它，我推荐你尝试一下今天提到的之前写到的某个实现。这个模式是一个易于学习的模式，同时也是一个威力巨大的模式。


## JavaScript 中介者模式

### 中介者模式
字典中中介者的定义是，一个中立方，在谈判和冲突解决过程中起辅助作用。在我们的世界，一个中介者是一个行为设计模式，使我们可以导出统一的接口，这样系统不同部分就可以彼此通信。

如果系统组件之间存在大量的直接关系，就可能是时候，使用一个中心的控制点，来让不同的组件通过它来通信。中介者通过将组件之间显式的直接的引用替换成通过中心点来交互的方式，来做到松耦合。这样可以帮助我们解耦，和改善组件的重用性。

在现实世界中，类似的系统就是，飞行控制系统。一个航站塔（中介者）处理哪个飞机可以起飞，哪个可以着陆，因为所有的通信（监听的通知或者广播的通知）都是飞机和控制塔之间进行的，而不是飞机和飞机之间进行的。一个中央集权的控制中心是这个系统成功的关键，也正是中介者在软件设计领域中所扮演的角色。

从实现角度来讲，中介者模式是观察者模式中的共享被观察者对象。在这个系统中的对象之间直接的发布/订阅关系被牺牲掉了，取而代之的是维护一个通信的中心节点。

也可以认为是一种补充-用于应用级别的通知，例如不同子系统之间的通信，子系统本身很复杂，可能需要使用发布/订阅模式来做内部组件之间的解耦。

另外一个类似的例子是 DOM 的事件冒泡机制，以及事件代理机制。如果系统中所有的订阅者都是对文档订阅，而不是对独立的节点订阅，那么文档就充当一个中介者的角色。DOM 的这种做法，不是将事件绑定到独立节点上，而是用一个更高级别的对象负责通知订阅者关于交互事件的信息。

### 基础的实现
中间人模式的一种简单的实现可以在下面找到,publish() 和 subscribe() 方法都被暴露出来使用:
```js
var mediator = (function () {

  // Storage for topics that can be broadcast or listened to
  var topics = {}

  // Subscribe to a topic, supply a callback to be executed
  // when that topic is broadcast to
  var subscribe = function (topic, fn) {

    if (!topics[topic]) {
      topics[topic] = []
    }

    topics[topic].push( { context: this, callback: fn })

    return this
  }

  // Publish/broadcast an event to the rest of the application
  var publish = function (topic) {

    var args 
    
    if (!topics[topic]) {
      return false
    }

    args = Array.prototype.slice.call( arguments, 1)
    for (var i = 0, l = topics[topic].length ; i < l; i++) {
      var subscription = topics[topic][i]
      subscription.callback.apply( subscription.context, args)      
    }

    return this
  }

  return {
    publish: publish,
    subscribe: subscribe,
    installTo: function(obj) {
      obj.subscribe = subscribe
      obj.publish = publish
    }
  }
}());
```

### 高级的实现

### 优点和缺点
中间人模式最大的好处就是，它节约了对象或者组件之间的通信信道，这些对象或者组件存在于从多对多到多对一的系统之中。由于解耦合水平的因素，添加新的发布或者订阅者是相对容易的。

也许使用这个模式最大的缺点是它可以引入一个单点故障。在模块之间放置一个中间人也可能会造成性能损失，因为它们经常是间接地的进行通信的。由于松耦合的特性，仅仅盯着广播很难去确认系统是如何做出反应的。

这就是说，提醒我们自己解耦合的系统拥有许多其它的好处，是很有用的——如果我们的模块互相之间直接的进行通信，对于模块的改变（例如：另一个模块抛出了异常）可以很容易的对我们系统的其它部分产生多米诺连锁效应。这个问题在解耦合的系统中很少需要被考虑到。

在一天结束的时候，紧耦合会导致各种头痛，这仅仅只是另外一种可选的解决方案，但是如果得到正确实现的话也能够工作得很好。

### 中间人VS观察者
开发人员往往不知道中间人模式和观察者模式之间的区别。不可否认，这两种模式之间有一点点重叠，但让我们回过头来重新寻求 GoF 的一种解释：

“在观察者模式中，没有封装约束的单一对象”。取而代之，观察者和主题必须合作来维护约束。通信的模式决定于观察者和主题相互关联的方式：一个单独的主题经常有许多的观察者，而有时候一个主题的观察者是另外一个观察者的主题。“

中间人和观察者都提倡松耦合，然而，中间人默认使用让对象严格通过中间人进行通信的方式实现松耦合。观察者模式则创建了观察者对象，这些观察者对象会发布触发对象认购的感兴趣的事件。

### 中间人VS门面
不久我们的描述就将涵盖门面模式，但作为参考之用，一些开发者也想知道中间人和门面模式之间有哪些相似之处。它们都对模块的功能进行抽象，但有一些细微的差别。

中间人模式让模块之间集中进行通信，它会被这些模块明确的引用。门面模式却只是为模块或者系统定义一个更加简单的接口，但不添加任何额外的功能。系统中其他的模块并不直接意识到门面的概念，而可以被认为是单向的。

## JavaScript 原型模式
### 原型模式
GoF 将原型模式引用为通过克隆的方式基于一个现有对象的模板创建对象的模式。

我们能够将原型模式认作是基于原型的继承中,我们创建作为其它对象原型的对象.原型对象自身被当做构造器创建的每一个对象的蓝本高效的使用着.如果构造器函数使用的原型包含例如叫做name的属性,那么每一个通过同一个构造器创建的对象都将拥有这个相同的属性。

在现存的(非 Javascript  的)语法中重新看一看对这个模式的定义,我们也许可以再一次发现对类的引用.真实的情况是那种原型继承避免了完全使用类.理论上既不是一个"定义的“对象，也不是一个核心对象。我们可以简单的创建现存函数型对象的拷贝。

使用原型模式的好处之一就是,我们在 JavaScript 提供的原生能力之上工作的,而不是 JavaScript 试图模仿的其它语言的特性.而对于其它的模式来说,情况并非如此。

这一模式不仅仅是实现继承的一种简单方式,它顺便还能够带来一点性能上的提升:当定义对象的一个方法时,它们都是使用引用创建的(因此所有的子对象都指向同一个函数),而不是创建属于它们的单独的拷贝。

对于那些有趣的,真正原型的集成,像 ECMAScript 5 标准中所定义的那样,需要使用 Object.create (如我们在本节的前面部分所见到的).为了提醒我们自己,Object.create 创建了一个拥有特定原型的对象,并且也包含选项式的特定属性.(例如,`Object.create(prototype,optionalDescriptorObject)`)。

我们可以在下面的示例中看到对这个的展示:
```js
var myCar = {
  name: "Ford Escort",

  drive: function () {
    console.log("Weeee. I'm driving!")
  },

  panic: function () {
    console.log("Wait. How do you stop this thing?")
  }
}

// Use Object.create to instantiate a new car
var yourCar = Object.create(myCar)

// Now we can see that one is a prototype of the other
console.log( yourCar.name )

// yourCar.name = 'Jeep'
console.log( yourCar)
```

Object.create 也允许我们简单的继承先进的概念,比如对象能够直接继承自其它对象,这种不同的继承.我们早先也看到 Object.create 允许我们使用供应的第二个参数来初始化对象属性。例如：
```js
var  vehicle = {
  getModel: function () {
    console.log("The model of this vehicle is.." + this.model)
  }
}

var car = Object.create(vehicle, {
  "model": {
    value: "Ford",
    // writable: false, configurable: false by default
    enumerable: true
  }
})

console.log(car)
```

这里的属性可以被 Object.create 的第二个参数来初始化,使用一种类似于我们前面看到的 Object.defineProperties 和 Object.defineProperties 方法所使用语法的对象字面值。

在枚举对象的属性,和(如 Crockford 所提醒的那样)在一个 hasOwnProperty() 检查中封装循环的内容时,原型关系会造成麻烦,这一事实是值得我们关注的。

如果我们希望在不直接使用 Object.create 的前提下实现原型模式,我们可以像下面这样,按照上面的示例,模拟这一模式:
```js
var vehiclePrototype = {

  init: function ( carModel ) {
    this.model = carModel;
  },

  getModel: function () {
    console.log( "The model of this vehicle is.." + this.model);
  }
};

function vehicle( model ) {

  function F() {};
  F.prototype = vehiclePrototype;

  var f = new F();

  f.init( model );
  return f;

}

var car = vehicle( "Ford Escort" );
car.getModel();
```

注意：这种可选的方式不允许用户使用相同的方式定义只读的属性(因为如果不小心的话 vehicle 原型可能会被改变)。

原型模式的最后一种可选实现可以像下面这样:
```js
var beget = (function () {

    function F() {}

    return function ( proto ) {
        F.prototype = proto;
        return new F();
    };
})();
```

一个人可以从 vehicle 函数引用这个方法,注意,这里的那个 vehicle 正是在模拟着构造器,因为原型模式在将一个对象链接到一个原型之外没有任何初始化的概念。



## JavaScript 命令模式

## JavaScript 外观模式

## JavaScript 工厂模式

## JavaScript Mixin模式

## JavaScript 装饰模式

## JavaScript 享元模式