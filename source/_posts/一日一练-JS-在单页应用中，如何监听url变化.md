---
title: 一日一练-JS 在单页应用中，如何监听url变化
date: 2022-01-29 11:25:02
tags:
  - 技术
  - 一日一练
  - JS
  - 性能
  - 转载
categories: 技术
---

本文转载自[在单页应用中，如何优雅的监听url的变化](https://github.com/forthealllight/blog/issues/37)，有部分删节，看原文请到原地址。

单页应用的原理从早起的根据 `url` 的 `hash` 变化，到根据 `H5` 的 `history` 的变化，实现无刷新条件下的页面重新渲染。那么在单页应用中是如何监听 `url` 的变化呢，本文将总结一下，如何在单页页面中优雅的监听 `url` 的变化。

* 单页应用原理
* 监听 `url` 中的 `hash` 变化
* 监听通过 `history` 来改变 `url` 的事件
* `replaceState` 和 `pushState` 行为的监听

<!--more-->

## 一、单页应用原理
单页应用的原理，在我们的上一篇文章中[React-Router源码阅读](https://github.com/forthealllight/blog/issues/26)已经讲的很详细，这里做一个简单介绍。单页应用使得页面可以在无刷新的条件下重新渲染，通过 `hash` 或者 `html5 Bom` 对象中的 `history` 可以做到改变 `url`，但是不刷新页面。

### (1)通过hash来实现单页路由
早期的前端路由是通过 `hash` 来实现的：

**改变 `url` 的 `hash` 值是不会刷新页面的。**

因此可以通过 `hash` 来实现前端路由，从而实现无刷新的效果。`hash` 属性位于 `location` 对象中，在当前页面中，可以通过：
```js
window.location.hash='edit'
```

来实现改变当前 `url` 的 `hash` 值。执行上述的 `hash` 赋值后，页面的 `url` 发生改变。

赋值前：`http://localhost:3000`
赋值后：`http://localhost:3000/#edit`

在 `url` 中多了以 `#` 结尾的 `hash` 值，但是赋值前后虽然页面的 `hash` 值改变导致页面完整的 `url` 发生了改变，但是页面是不会刷新的。

此外，除了可以通过 `window.location.hash` 来改变当前页面的 `hash` 值外，还可以通过 `html` 的 `a`标签来实现：
```html
<a href="#edit">edit</a>
```

### (2)通过history实现前端路由
`HTML5` 的 `History` 接口，`History` 对象是一个底层接口，不继承于任何的接口。`History` 接口允许我们操作浏览器会话历史记录。

`History` 提供了一些属性和方法。

`History` 的属性：

* `History.length`: 返回在会话历史中有多少条记录，包含了当前会话页面。此外如果打开一个新的 `Tab`，那么这个 `length` 的值为 1
* `History.state`:
保存了会触发 `popState` 事件的方法，所传递过来的属性对象（后面会在 `pushState` 和 `replaceState` 方法中详细的介绍）

History方法：

* `History.back()`: 返回浏览器会话历史中的上一页，跟浏览器的回退按钮功能相同

* `History.forward()`: 指向浏览器会话历史中的下一页，跟浏览器的前进按钮相同

* `History.go()`: 可以跳转到浏览器会话历史中的指定的某一个记录页

* `History.pushState()`: `pushState` 可以将给定的数据压入到浏览器会话历史栈中，该方法接收 3 个参数，对象，`title` 和一串 `url`。`pushState` 后会改变当前页面 `url`，但是不会伴随着刷新

* `History.replaceState()`: `replaceState` 将当前的会话页面的 `url` 替换成指定的数据，`replaceState` 后也会改变当前页面的 `url`，但是也不会刷新页面。

上面的方法中，`pushState` 和 `repalce` 的相同点：

**就是都会改变当前页面显示的 url，但都不会刷新页面。**

不同点：

**`pushState` 是压入浏览器的会话历史栈中，会使得 `History.length` 加 1，而 `replaceState` 是替换当前的这条会话历史，因此不会增加 `History.length`.**

### (3)总结
通过改变 `hash` 值，或者 `history` 的 `repalceState` 和 `pushState` 都可以实现无刷新的改变 `url`。这样还留有一个问题需要解决：

**如何监听 `url` 的改变**

因为我们不仅要无刷新的改变 `url`，还要监听到这个 `url` 改变的行为，根据该行为去重新渲染视图。在下几章中，重点介绍一下如何监听 `url` 的改变。

## 二、监听url中的hash变化
通过 `hash` 改变了`url`，会触发 `hashchange` 事件，只要监听 `hashchange` 事件，就能捕获到通过 `hash` 改变 `url` 的行为。
```js
window.onhashchange=function(event){
  console.log(event);
}
//或者
window.addEventListener('hashchange',function(event){
   console.log(event);
})
```

当 `hash` 值改变时，输出一个 `HashChangeEvent`。该 `HashChangeEvent` 的具体值为：
```js
{isTrusted: true, oldURL: "http://localhost:3000/", newURL:   "http://localhost:3000/#teg", type: "hashchange"}
```

有了监听事件，且改变 `hash` 页面不刷新，这样我们就可以在监听事件的回调函数中，执行我们展示和隐藏不同 UI 显示的功能，从而实现前端路由。

## 三、监听通过history来改变url的事件
在上一章讲到了通过 `History` 改变 `url` 有以下几种方法：`History.back()`、`History.forward()`、`History.go()`、`History.pushState()` 和 `History.replaceState()`。

同时在 `history` 中还支持一个事件，该事件为 `popstate`。第一想法就是如果 `popstate` 能够监听所有的 `history` 方法所导致的 `url` 变化，那么就大功告成了。遗憾的是：

`History.back()`、`History.forward()`、`History.go()` 事件是会触发 `popstate` 事件的，但是`History.pushState()` 和 `History.replaceState()` 不会触发 `popstate` 事件。

如果是 `History.back()`、`History.forward()`、`History.go()` 那么会触发 `popstate` 事件，我们只需要：
```js
window.addEventListener('popstate', function(event) {
     console.log(event);
})
```

就可以监听到相应的行为，手动调用：
```js
window.history.go();
window.history.back();
window.history.forward();
```

都会触发这个事件，此外，在浏览器中点击后退和前进按钮也会触发 `popstate` 事件，这个事件内容为
```js
PopStateEvent {isTrusted: true, state: null, type: "popstate", target: Window, currentTarget: Window, …}
```

但是，`History.pushState()` 和 `History.replaceState()` 不会触发 `popstate` 事件，举例来说：
```js
window.addEventListener('popstate', function(event) {
     console.log(event);
})
window.history.pushState({first:'first'}, "page 2", "/first"})
```

上述例子中不会有任何的输出，因为并没有监听的 `popstate` 事件的发生。

`History.go()` 和 `History.back()` 等，虽然可以触发 `popstate` 事件，但是都会刷新页面，我们在单页应用中使用的是 `replaceState` 和 `pushState`，因此这里还有一个等待解决的问题：

**如何监听 `replaceState` 和 `pushState` 行为**

## 四、replaceState和pushState行为的监听
在上面的例子中我们发现 `History.replaceState` 和 `pushState` 确实不会触发 popstate事件，那么如何监听这两个行为呢。可以通过在方法里面主动的去触发popState事件。另一种就是在方法中创建一个新的全局事件。

具体做法为：
```js
var _wr = function(type) {
    var orig = history[type];
    return function() {
        var rv = orig.apply(this, arguments);
        var e = new Event(type);
        e.arguments = arguments;
        window.dispatchEvent(e);
        return rv;
   };
};
history.pushState = _wr('pushState');
history.replaceState = _wr('replaceState');
```
> 测试了一下 `popstate` 和 `hashchange` 这两个会同时触发，无论是 `window.location.hash='123'`还是 `History.back()`, `History.forward()`、`History.go()` 都会同时触发这两个事件的。
`popstate` 在 `webkit` 内核浏览器中就是这样的，可以说是 `webkit` 的 BUG。
传送门：https://stackoverflow.com/questions/6421769/popstate-on-pages-load-in-chrome


这样就创建了2个全新的事件，事件名为 `pushState` 和 `replaceState`，我们就可以在全局监听：
```js
window.addEventListener('replaceState', function(e) {
  console.log('THEY DID IT AGAIN! replaceState 111111');
});
window.addEventListener('pushState', function(e) {
  console.log('THEY DID IT AGAIN! pushState 2222222');
});
```

这样就可以监听到pushState和replaceState行为。

参考文章：https://stackoverflow.com/questions/4570093/how-to-get-notified-about-changes-of-the-history-via-history-pushstate


