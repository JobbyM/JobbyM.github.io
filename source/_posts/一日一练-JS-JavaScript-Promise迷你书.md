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
本章我们将会学习 Promise 提供的各种方法以及如何进行错误处理。

### Promise.resolve
一般情况下我们都会使用 new Promise() 来创建 promise 对象，但是除此之外我们也可以使用其他方法。

在这里，我们将会学习如何使用 Promise.resolve 和 Promise.reject这两个方法。

#### new Promise的快捷方式
静态方法 Promise.resolve(value) 可以认为是 new Promise() 方法的快捷方式。

比如 Promise.resolve(42); 可以认为是以下代码的语法糖。
```js
new Promise(function(resolve){
    resolve(42);
});
```

在这段代码中的 resolve(42); 会让这个 promise 对象立即进入确定（即resolved）状态，并将 42 传递给后面 then 里所指定的 onFulfilled 函数。

方法 Promise.resolve(value); 的返回值也是一个promise对象，所以我们可以像下面那样接着对其返回值进行 .then 调用。
```js
Promise.resolve(42).then(function(value){
    console.log(value);
});
```

Promise.resolve 作为 new Promise() 的快捷方式，在进行 promise 对象的初始化或者编写测试代码的时候都非常方便。

#### Thenable
Promise.resolve 方法另一个作用就是将 thenable 对象转换为 promise 对象。

ES6 Promises 里提到了 Thenable 这个概念，简单来说它就是一个非常类似 promise 的东西。

就像我们有时称具有 .length 方法的非数组对象为 Array like 一样，thenable 指的是一个具有 .then 方法的对象。

这种将 thenable 对象转换为 promise 对象的机制要求 thenable 对象所拥有的 then 方法应该和 Promise 所拥有的 then 方法具有同样的功能和处理过程，在将 thenable 对象转换为 promise 对象的时候，还会巧妙的利用 thenable 对象原来具有的 then 方法。

到底什么样的对象能算是 thenable 的呢，最简单的例子就是 jQuery.ajax()，它的返回值就是 thenable 的。

因为 jQuery.ajax() 的返回值是 jqXHR Object 对象，这个对象具有 .then 方法。
```js
$.ajax('/json/comment.json');// => 拥有 `.then` 方法的对象
```

这个 thenable 的对象可以使用 Promise.resolve 来转换为一个 romise 对象。

变成了 promise 对象的话，就能直接使用 then 或者 catch 等这些在 ES6 Promises 里定义的方法了。

**将thenable对象转换promise对象**
```js
var promise = Promise.resolve($.ajax('/json/comment.json'));// => promise对象
promise.then(function(value){
   console.log(value);
});
```

Promise.resolve 只使用了共通的方法 then ，提供了在不同的类库之间进行 promise 对象互相转换的功能。

这种转换为 thenable 的功能在之前是通过使用 Promise.cast 来完成的，从它的名字我们也不难想象它的功能是什么。

除了在编写使用 Promise 的类库等软件时需要对 Thenable 有所了解之外，通常作为end-user 使用的时候，我们可能不会用到此功能。

简单总结一下 Promise.resolve 方法的话，可以认为它的作用就是将传递给它的参数填充（Fulfilled）到 promise 对象后并返回这个 promise 对象。

此外，Promise 的很多处理内部也是使用了 Promise.resolve 算法将值转换为 promise 对象后再进行处理的。

### Promise.reject
Promise.reject(error) 是和 Promise.resolve(value) 类似的静态方法，是 new Promise() 方法的快捷方式。

比如 Promise.reject(new Error("出错了")) 就是下面代码的语法糖形式。
```js
new Promise(function(resolve,reject){
    reject(new Error("出错了"));
});
```

这段代码的功能是调用该 promise 对象通过 then 指定的 onRejected 函数，并将错误（Error）对象传递给这个 onRejected 函数。
```js
Promise.reject(new Error("BOOM!")).catch(function(error){
    console.error(error);
});
```

它和 Promise.resolve(value) 的不同之处在于 promise 内调用的函数是 reject 而不是 resolve，这在编写测试代码或者进行 debug 时，说不定会用得上。

### 专栏: Promise只能进行异步操作？
在使用 Promise.resolve(value) 等方法的时候，如果 promise 对象立刻就能进入resolve 状态的话，那么你是不是觉得 .then 里面指定的方法就是同步调用的呢？

实际上， .then 中指定的方法调用是异步进行的。
```js
var promise = new Promise(function (resolve){
    console.log("inner promise"); // 1
    resolve(42);
});
promise.then(function(value){
    console.log(value); // 3
});
console.log("outer promise"); // 2
```

执行上面的代码会输出下面的 log，从这些 log 我们清楚地知道了上面代码的执行顺序。
```js
inner promise // 1
outer promise // 2
42            // 3
```

由于 JavaScript 代码会按照文件的从上到下的顺序执行，所以最开始 `<1>` 会执行，然后是 `resolve(42);` 被执行。这时候 `promise` 对象的已经变为确定状态，FulFilled 被设置为了 `42` 。

下面的代码 `promise.then` 注册了 `<3>` 这个回调函数，这是本专栏的焦点问题。

由于 `promise.then` 执行的时候promise对象已经是确定状态，从程序上说对回调函数进行同步调用也是行得通的。

但是即使在调用 `promise.then` 注册回调函数的时候 `promise` 对象已经是确定的状态，`Promise` 也会以异步的方式调用该回调函数，这是在 `Promise` 设计上的规定方针。

因此 `<2>` 会最先被调用，最后才会调用回调函数 `<3>` 。

为什么要对明明可以以同步方式进行调用的函数，非要使用异步的调用方式呢？

#### 同步调用和异步调用同时存在导致的混乱
其实在 Promise 之外也存在这个问题，这里我们以一般的使用情况来考虑此问题。

这个问题的本质是接收回调函数的函数，会根据具体的执行情况，可以选择是以同步还是异步的方式对回调函数进行调用。

下面我们以 onReady(fn) 为例进行说明，这个函数会接收一个回调函数进行处理。
```js 
// mixed-onready.js
function onReady(fn) {
    var readyState = document.readyState;
    if (readyState === 'interactive' || readyState === 'complete') {
        fn();
    } else {
        window.addEventListener('DOMContentLoaded', fn);
    }
}
onReady(function () {
    console.log('DOM fully loaded and parsed');
});
console.log('==Starting==');
```
mixed-onready.js 会根据执行时DOM是否已经装载完毕来决定是对回调函数进行同步调用还是异步调用。

**如果在调用onReady之前DOM已经载入的话**
对回调函数进行同步调用

**如果在调用onReady之前DOM还没有载入的话**
通过注册 DOMContentLoaded 事件监听器来对回调函数进行异步调用

因此，如果这段代码在源文件中出现的位置不同，在控制台上打印的log消息顺序也会不同。

为了解决这个问题，我们可以选择统一使用异步调用的方式。
```js
// async-onready.js
function onReady(fn) {
    var readyState = document.readyState;
    if (readyState === 'interactive' || readyState === 'complete') {
        setTimeout(fn, 0);
    } else {
        window.addEventListener('DOMContentLoaded', fn);
    }
}
onReady(function () {
    console.log('DOM fully loaded and parsed');
});
console.log('==Starting==');
```

前面我们看到的 promise.then 也属于此类，为了避免上述中同时使用同步、异步调用可能引起的混乱问题，Promise 在规范上规定 **Promise 只能使用异步调用方式** 。

最后，如果将上面的 onReady 函数用 Promise 重写的话，代码如下面所示。
```js
function onReadyPromise() {
  return new Promise(function(resolve, reject) {
    var readyState = document.readyState
    if (readyState === 'interactive' || readyState === 'complete') {
      resolve()
    } else {
      window.addEventListener('DOMContentLoaded', resolve)
    }
  })
}

onReadyPromise().then(function () {
  console.log('DOM fully loaded and parsed');
});
console.log('==Starting==')
```

由于 Promise 保证了每次调用都是以异步方式进行的，所以我们在实际编码中不需要调用 setTimeout 来自己实现异步调用。

### Promise#then
在前面的章节里我们对 Promise 基本的实例方法 then 和 catch 的使用方法进行了说明。

这其中，我想大家已经认识了 .then().catch() 这种链式方法的写法了，其实在 Promise 里可以将任意个方法连在一起作为一个方法链（method chain）。

**promise 可以写成方法链的形式**
```js
aPromise.then(function taskA(value){
// task A
}).then(function taskB(vaue){
// task B
}).catch(function onRejected(error){
    console.log(error);
});
```

如果把在 then 中注册的每个回调函数称为 task 的话，那么我们就可以通过 Promise 方法链方式来编写能以 task A → task B 这种流程进行处理的逻辑了。

Promise 方法链这种叫法有点长（其实是在日语里有点长，中文还可以 --译者注），因此后面我们会简化为 promise chain 这种叫法。

Promise 之所以适合编写异步处理较多的应用，promise chain 可以算得上是其中的一个原因吧。

在本小节，我们将主要针对使用 then 的 promise chain 的行为和流程进行学习。

#### promise chain
在这里我们看到了一个很简单的 then → catch 的例子，如果我们将方法链的长度变得更长的话，那在每个 promise 对象中注册的 onFulfilled 和 onRejected 将会怎样执行呢？

> promise chain - 即方法链越短越好。 在这个例子里我们是为了方便说明才选择了较长的方法链。

我们先来看看下面这样的 promise chain。
```js
// promise-then-catch-flow.js
function taskA() {
    console.log("Task A");
}
function taskB() {
    console.log("Task B");
}
function onRejected(error) {
    console.log("Catch Error: A or B", error);
}
function finalTask() {
    console.log("Final Task");
}

var promise = Promise.resolve();
promise
    .then(taskA)
    .then(taskB)
    .catch(onRejected)
    .then(finalTask);
```

上面代码中的 promise chain 的执行流程，如果用一张图来描述一下的话，像下面的图那样。
{% asset_img 3.png %}

在 上述代码 中，我们没有为 then 方法指定第二个参数(onRejected)，也可以像下面这样来理解。

* then
注册 onFulfilled 时的回调函数

* catch
注册 onRejected 时的回调函数

再看一下 上面的流程图 的话，我们会发现 Task A 和 Task B 都有指向 onRejected 的线出来。

这些线的意思是在 Task A 或 Task B 的处理中，在下面的情况下就会调用 onRejected 方法。

* 发生异常的时候

* 返回了一个Rejected状态的promise对象

在 第一章 中我们已经看到，Promise中的处理习惯上都会采用 try-catch 的风格，当发生异常的时候，会被 catch 捕获并被由在此函数注册的回调函数进行错误处理。

另一种异常处理策略是通过 返回一个 Rejected 状态的 promise 对象 来实现的，这种方法不通过使用 throw 就能在 promise chain 中对 onRejected 进行调用。

关于这种方法由于和本小节关系不大就不在这里详述了，大家可以参考一下第4章 使用reject 而不是 throw 中的内容。

此外在 promise chain 中，由于在 onRejected 和 Final Task 后面没有 catch 处理了，因此在这两个 Task 中如果出现异常的话将不会被捕获，这点需要注意一下。

下面我们再来看一个具体的关于 Task A → onRejected 的例子。

**Task A产生异常的例子**
Task A 处理中发生异常的话，会按照 TaskA → onRejected → FinalTask 这个流程来进行处理。
{% asset_img 4.png %}

将上面流程写成代码的话如下所示。
```js
// promise-then-taska-throw.js
function taskA() {
    console.log("Task A");
    throw new Error("throw Error @ Task A")
}
function taskB() {
    console.log("Task B");// 不会被调用
}
function onRejected(error) {
    console.log(error);// => "throw Error @ Task A"
}
function finalTask() {
    console.log("Final Task");
}

var promise = Promise.resolve();
promise
    .then(taskA)
    .then(taskB)
    .catch(onRejected)
    .then(finalTask);
```

执行这段代码我们会发现 Task B 是不会被调用的。
> 在本例中我们在 taskA 中使用了 throw 方法故意制造了一个异常。但在实际中想主动进行 onRejected 调用的时候，应该返回一个 Rejected 状态的 promise 对象。关于这种两种方法的异同，请参考 使用 reject 而不是 throw 中的讲解。

#### promise chain 中如何传递参数
前面例子中的 Task 都是相互独立的，只是被简单调用而已。

这时候如果 Task A 想给 Task B 传递一个参数该怎么办呢？

答案非常简单，那就是在 Task A 中 return 的返回值，会在 Task B 执行时传给它。

我们还是先来看一个具体例子吧。
```js
// promise-then-passing-value.js
function doubleUp(value) {
    return value * 2;
}
function increment(value) {
    return value + 1;
}
function output(value) {
    console.log(value);// => (1 + 1) * 2
}

var promise = Promise.resolve(1);
promise
    .then(increment)
    .then(doubleUp)
    .then(output)
    .catch(function(error){
        // promise chain中出现异常的时候会被调用
        console.error(error);
    });
```

这段代码的入口函数是 `Promise.resolve(1);` ，整体的 promise chain 执行流程如下所示。

1. `Promise.resolve(1);` 传递 1 给 increment 函数

2. 函数 increment 对接收的参数进行 +1 操作并返回（通过return）

3. 这时参数变为 2，并再次传给 doubleUp 函数

4. 最后在函数 output 中打印结果
{% asset_img 5.png %}

每个方法中 return 的值不仅只局限于字符串或者数值类型，也可以是对象或者 promise 对象等复杂类型。

return 的值会由 `Promise.resolve(return的返回值);` 进行相应的包装处理，因此不管回调函数中会返回一个什么样的值，最终 then 的结果都是返回一个新创建的 promise 对象。
> 关于这部分内容可以参考 专栏: 每次调用 then 都会返回一个新创建的 promise 对象 ，那里也对一些常见错误进行了介绍。

也就是说， `Promise#then` 不仅仅是注册一个回调函数那么简单，它还会将回调函数的返回值进行变换，创建并返回一个 promise 对象。


## Chapter.3 - Promise测试

## Chapter.4 - Advanced

## Promises API Reference