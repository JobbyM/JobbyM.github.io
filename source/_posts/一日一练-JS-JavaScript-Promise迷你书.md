---
title: 一日一练-JS JavaScript Promise迷你书
date: 2021-06-29 15:48:32
tags:
  - 技术
  - 一日一练
  - JS
  - Promise
  - 转载
categories: 技术
---

本文内容摘自[《JavaScript Promise迷你书》](http://liubin.org/promises-book/#introduction)，有删节和自己的理解，需要的可以看原文。

<!--more-->

## Chapter.1 - 什么是Promise

### 什么是Promise、
Promise 是抽象异步处理对象以及对其进行各种操作的组件。

Promise 是把异步处理对象和处理规则进行规范化， 并按照采用统一的接口来编写，而采取规定方法之外的写法都会出错。
```js
var promise = getAsyncPromise("fileA.txt"); 
promise.then(function(result){
    // 获取文件内容成功时的处理
}).catch(function(error){
    // 获取文件内容失败时的处理
});
```

我们可以向这个预设了抽象化异步处理的 promise 对象， 注册这个 promise 对象执行成功时和失败时相应的回调函数。

这和回调函数方式相比有哪些不同之处呢？ 在使用 promise 进行一步处理的时候，我们必须按照接口规定的方法编写处理代码。

也就是说，除 promise 对象规定的方法(这里的 then 或 catch) 以外的方法都是不可以使用的， 而不会像回调函数方式那样可以自己自由的定义回调函数的参数，而必须严格遵守固定、统一的编程方式来编写代码。

这样，基于 Promise 的统一接口的做法， 就可以形成基于接口的各种各样的异步处理模式。

所以，promise 的功能是可以将复杂的异步处理轻松地进行模式化， 这也可以说得上是使用 promise 的理由之一。

### Promise简介

#### Constructor
从构造函数 Promise 来创建一个新建新 promise 对象作为接口。

要想创建一个 promise 对象、可以使用 new 来调用 Promise 的构造器来进行实例化。
```js
var promise = new Promise(function(resolve, reject) {
    // 异步处理
    // 处理结束后、调用resolve 或 reject
});
```

#### Instance Method 
对通过 new 生成的 promise 对象为了设置其值在 resolve(成功) / reject(失败)时调用的回调函数 可以使用 promise.then() 实例方法。
```js
promise.then(onFulfilled, onRejected)
```

**resolve(成功)时** 
onFulfilled 会被调用

**reject(失败)时**
onRejected 会被调用

onFulfilled、onRejected 两个都为可选参数。

promise.then 成功和失败时都可以使用。 另外在只想对异常进行处理时可以采用 promise.then(undefined, onRejected) 这种方式，只指定reject时的回调函数即可。 不过这种情况下 promise.catch(onRejected) 应该是个更好的选择。
```js
promise.catch(onRejected)
```

#### Static Method

像 Promise 这样的全局对象还拥有一些静态方法。

包括 Promise.all() 还有 Promise.resolve() 等在内，主要都是一些对Promise进行操作的辅助方法。

#### Promise workflow
```js
function asyncFunction () {
  // 	new Promise 构造器之后，会返回一个 promise 对象
  return new Promise(function(resolve, reject) {
    setTimeout(function(){
      resolve('Async Hello world')
    }, 16)
  })
}

// 为promise对象用设置 .then 调用返回值时的回调函数
asyncFunction().then(function(value) {
  console.log(value) // => `Async Hello world`
}).catch(function(error) {
  console.log(error)
})
```

asyncFunction 这个函数会返回 promise 对象， 对于这个 promise 对象，我们调用它的 then 方法来设置 resolve 后的回调函数， catch 方法来设置发生错误时的回调函数。

该 promise 对象会在 setTimeout 之后的 16ms 时被 resolve, 这时 then 的回调函数会被调用，并输出 'Async Hello world' 。

在这种情况下 catch 的回调函数并不会被执行（因为 promise 返回了 resolve）， 不过如果运行环境没有提供 setTimeout 函数的话，那么上面代码在执行中就会产生异常，在 catch 中设置的回调函数就会被执行。

####  Promise的状态
我们已经大概了解了 Promise 的处理流程，接下来让我们来稍微整理一下 Promise 的状态。

用 new Promise 实例化的 promise 对象有以下三个状态。

**"has-resolution" - Fulfilled**
resolve(成功)时。此时会调用 onFulfilled

**"has-rejection" - Rejected**
reject(失败)时。此时会调用 onRejected

**"unresolved" - Pending**
既不是 resolve 也不是 reject 的状态。也就是 promise 对象刚被创建后的初始化状态等

关于上面这三种状态的读法，其中 左侧为在 ES6 Promises 规范中定义的术语， 而右侧则是在 Promises/A+ 中描述状态的术语。

{% asset_img 1.png %}

promise 对象的状态，从 Pending 转换为 Fulfilled 或 Rejected 之后， 这个promise 对象的状态就不会再发生任何变化。

也就是说，Promise 与 Event 等不同，在 .then 后执行的函数可以肯定地说只会被调用一次。

另外，Fulfilled 和 Rejected 这两个中的任一状态都可以表示为 **Settled(不变的)**。

**Settled**
resolve(成功) 或 reject(失败)。

从 Pending 和 Settled 的对称关系来看，Promise 状态的种类/迁移是非常简单易懂的。

当 promise 的对象状态发生变化时，用 .then 来定义只会被调用一次的函数。

### 编写Promise代码

#### 创建promise对象
创建 promise 对象的流程如下所示。

1. new Promise(fn) 返回一个promise对象

2. 在fn 中指定异步等处理

    * 处理结果正常的话，调用resolve(处理结果值)

    * 处理结果错误的话，调用reject(Error对象)

按这个流程我们来实际编写下 promise 代码吧。

我们的任务是用 Promise 来通过异步处理方式来获取 XMLHttpRequest(XHR) 的数据。

**创建XHR的promise对象**
首先，创建一个用 Promise 把 XHR 处理包装起来的名为 getURL 的函数。
```js
// xhr-promise.js
function getURL(URL) {
  return new Promise(function(resolve, reject){
    var req = new XMLHttpRequest()
    req.open('GET', URL, true)
    req.onload = function () {
      if (req.status === 200) {
        resolve(req.responseText)
      } else {
        reject(new Error(req.statusText))
      }
    }
    req.onerror = function () {
      reject(new Error(req.statusText))
    }
    req.send()
  })
}
// 运行实例
var URL = "http://httpbin.org/get"
getURL(URL).then(function onFullfilled(value){
  console.log(value)
}).catch(function onRejected(error){
  console.error(error)
})
```

#### 编写promise对象处理方法
让我们在实际中使用一下刚才创建的返回 promise 对象的函数
```js
getURL("http://example.com/"); // => 返回promise对象
```

promise 对象拥有几个实例方法， 我们使用这些实例方法来为 promise 对象创建依赖于 promise 的具体状态、并且只会被执行一次的回调函数。

为 promise 对象添加处理方法主要有以下两种

* promise 对象被 resolve 时的处理(onFulfilled)

* promise 对象被 reject 时的处理(onRejected)

{% asset_img 2.png %}

首先，我们来尝试一下为 getURL 通信成功并取到值时添加的处理函数。

此时所谓的 通信成功 ， 指的就是在被 resolve 后， promise 对象变为 FulFilled 状态 。

被 resolve 后的处理，可以在 .then 方法中传入想要调用的函数。
```js
var URL = "http://httpbin.org/get"
getURL(URL).then(function onFullfilled(value){  // 为了方便理解我们把函数命名为 onFulfilled
  console.log(value)
})
```

getURL 函数 中的 resolve(req.responseText); 会将 promise 对象变为 resolve（Fulfilled）状态， 同时使用其值调用 onFulfilled 函数。

不过目前我们还没有对其中可能发生的错误做任何处理， 接下来，我们就来为 getURL 函数添加发生错误时的异常处理。

此时 发生错误 ， 指的也就是 reject 后 promise 对象变为 Rejected 状态 。

被 reject 后的处理，可以在 .then 的第二个参数 或者是在 .catch 方法中设置想要调用的函数。

把下面 reject 时的处理加入到刚才的代码，如下所示。
```js
var URL = "http://httpbin.org/status/500"   // 服务端返回的状态码为500
getURL(URL).then(function onFullfilled(value){ 
  console.log(value)
}).catch(funtion onRejected(error){ // 为了方便理解函数被命名为 onRejected
  console.error(error)
})
```

在 getURL 的处理中发生任何异常，或者被明确 reject 的情况下， 该异常原因（Error对象）会作为 .catch 方法的参数被调用。

其实 .catch 只是 promise.then(undefined, onRejected) 的别名而已， 如下代码也可以完成同样的功能。
```js
getURL(URL).then(onFulfilled, onRejected); // onFulfilled, onRejected 是和刚才相同的函数
```

**总结**
在本章我们简单介绍了以下内容：

* 用 new Promise 方法创建 promise 对象

* 用 .then 或 .catch 添加 promise 对象的处理函数

到此为止我们已经学习了 Promise 的基本写法。 其他很多处理都是由此基本语法延伸的，也使用了 Promise 提供的一些静态方法来实现。

实际上即使使用回调方式的写法也能完成上面同样的工作，而使用 Promise 方式的话有什么优点么？在本小节中我们没有讲到两者的对比及 Promise 的优点。在接下来的章节中，我们将会对 Promise 优点之一，即错误处理机制进行介绍，以及和传统的回调方式的对比。

## Chapter.2 - 实战Promise

## Chapter.3 - Promise测试

## Chapter.4 - Advanced

## Promises API Reference