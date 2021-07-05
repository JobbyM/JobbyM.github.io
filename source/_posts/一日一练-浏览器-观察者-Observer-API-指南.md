---
title: 一日一练-浏览器 观察者 Observer API 指南
date: 2021-07-02 18:18:15
tags:
  - 技术
  - 观察者
  - 转载
categories: 技术
---

> 子曰：万里之行，始于足下。

本文是根据CSDN上的这篇文章 [现代浏览器观察者 Observer API 指南](https://blog.csdn.net/weixin_40906515/article/details/102735722) 实现的

<!--more-->

## 前言
前段时间在研究前端异常监控/埋点平台的实现。

在思考方案时，想到了浏览器自带的观察者以及页面生命周期API 。

于是在翻查资料时意外发现，原来现代浏览器支持多达四种不同类型的观察者：

* Intersection Observer，交叉观察者。

* Mutation Observer，变动观察者。

* Resize Observer，视图观察者。

* Performance Observer，性能观察者

{% asset_img 1.png %}

## IntersectionObserver：交叉观察者
IntersectionObserver 接口，提供了一种异步观察目标元素与其祖先元素或顶级文档视窗( viewport )交叉状态的方法，祖先元素与视窗( viewport )被称为根( root )

### 出现的意义
想要计算 Web 页面的元素的位置，非常依赖于 DOM 状态的显式查询。但这些查询是同步的，会导致昂贵的样式计算开销（重绘和回流），且不停轮询会导致大量的性能浪费。
{% asset_img 2.png %}

于是便发展了以下的几种方案：
* 构建 DOM 和数据的自定义预加载和延迟加载。

* 实现了数据绑定的高性能滚动列表，该列表加载和呈现数据集的子集。

* 通过scroll等事件或通过插件的形式，计算真实元素可见性。

而它们都有几项共同特点：

1. 基本实现形式都是查询各个元素相对与某些元素（全局视口）的“被动查询”。

2. 信息可以异步传递（例如从另一个线程传递），且没有统一捕获错误的处理。

3. web 平台支持匮乏，各有各家的处理。需要开发人员消耗大量精力兼容。

### IntersectionObserver 的优势
Intersection Observer API通过为开发人员提供一种新方法来异步查询元素相对于其他元素或全局视口的位置，从而解决了上述问题:

* **异步处理**消除了昂贵的 DOM 和样式查询，连续轮询以及使用自定义插件的需求。

* 通过消除对这些方法的需求，可以使应用程序显着降低 CPU，GPU 和资源成本。

### IntersectionObserver 基本使用
使用 IntersectionObserver API 主要需要三个步骤：
1. 创建观察者

2. 定义回调事件

3. 定义要观察的目标对象

#### 创建观察者
```js
const options = {
  root: document.querySelector('.scrollContainer'),
  rootMargin: '0px',
  threshold: [0.3, 0.5, 0.8, 1]
}

const observer = new IntersectionObserver(handler, options)
```

这几个参数用大白话解释就是：

1. root：指定一个根元素

2. rootMargin：使用类似于设置 CSS 边距的语法来指定根边距（根元素的观察影响范围）
{% asset_img 3.png %}

3. threshold: 阈值，可以为数组。[0.3] 意味着，当目标元素在根元素指定的元素内可见 30% 时，调用处理函数。

#### 定义回调事件
当目标元素与根元素通过阈值相交时，就会触发回调函数。
```js
function handler (entries, observer) {
  entries.forEach(entry => {
    // 每个成员都是一个 IntersectionObserverEntry 对象。
    // 举例来说，如果同时有两个被观察的对象的可见性发生变化，entries 数组就会有两个成员
    // entry.boudingClientRect
    // entry.intersectionRatio
    // entry.intersectionRect
    // entry.isIntersection
    // entry.rootBounds
    // entry.target
    // entry.time
  })
}
```

* time 时间戳-

* rootBounds 根元素的位置

* boundingClientRect 目标元素的位置信息

* intersectionRect 交叉部分的位置信息

* intersectionRatio 目标元素的可见比例，看下图示

* target。

{% asset_img 4.png %}

#### 定义要观察的目标对象
任何目标元素都可以通过调用 `.observer(target)` 方法来观察。
```js
const target = document.querySelector('.targetBox')
observer.observe(target)
```

此外，还有两个方法：

停止对某目标的监听
```js
observer.unobserve(target)
```

终止对所有目标的监听
```js
observer.disconnect()
```

#### 例子1：图片懒加载
HTML:
```html
<img src="placeholder.png" data-src="img-1.jpg">
<img src="placeholder.png" data-src="img-2.jpg">
<img src="placeholder.png" data-src="img-3.jpg">
<!-- more images -->
```

脚本：
```js
let observer = new IntersectionObserver(
  (entries, observer) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        // 替换属性
        entry.target.src = entry.target.dataset.src
        observer.unobserve(entry.target)
      }
    })
  },
  { rootMargin: "0px 0px -200px 0px"}
)

document.querySelectorAll('img').forEach(img => { observer.observe(img)})
```

上述例子表示 仅在到达视口距离底部200px时加载图片。

#### 例子2：兴趣埋点
关于兴趣埋点，一个比较通用的方案是：

>来自：《超好用的API之IntersectionObserver》

```js
const boxList = [...document.querySelectorAll('.box')]
 
var io = new IntersectionObserver((entries) =>{
  entries.forEach(item => {
    // intersectionRatio === 1说明该元素完全暴露出来，符合业务需求
    if (item.intersectionRatio === 1) {
      // 。。。 埋点曝光代码
      io.unobserve(item.target)
    }
  })
}, {
  root: null,
  threshold: 1, // 阀值设为1，当只有比例达到1时才触发回调函数
})
 
// observe遍历监听所有box节点
boxList.forEach(box => io.observe(box))
```

至于怎样评断用户是否感兴趣，记录方式就见仁见智了：

* 位于屏幕中间，并停留时长大于 2 秒，计数一次。

* 区域悬停，触发定时器记录时间。

* PC 端记录鼠标点击次数/悬停时间，移动端记录 touch 事件

#### 控制动画/视频 执行
HTML：
```html
<video src="OSRO-animation.mp4" controls=""></video>
```

JS：
```js
let video = document.querySelector('video');
let isPaused = false; /* Flag for auto-paused video */
let observer = new IntersectionObserver((entries, observer) => { 
  entries.forEach(entry => {
    if(entry.intersectionRatio!=1  && !video.paused){
      video.pause(); isPaused = true;
    }
    else if(isPaused) {video.play(); isPaused=false}
  });
}, {threshold: 1});
observer.observe(video);
```

效果：
{% asset_img 5.gif %}


## Mutation Observer：变动观察者
接口提供了监视对 DOM 树所做更改的能力。它被设计为旧的 MutationEvents 功能的替代品，该功能是 DOM3 Events 规范的一部分。

### 出现的意义

归根究底，是 MutationEvents 的功能不尽人意：
1. 在 MDN 中也写到了，是被 DOM Event 承认在 API 上有缺陷，反对使用。

2. 核心缺陷是：性能问题和跨浏览器支持。

3. 为 DOM 添加 mutation 监听器极度降低进一步修改 DOM 文档的性能（慢1.5 - 7倍），此外, 移除监听器不会逆转的损害。

> 来自：《监听DOM加载完成及改变——MutationObserver应用》

MutationEvents 的原理：通过绑定事件监听 DOM

乍一看到感觉很正常，那列一下相关监听的事件：
```
DOMAttributeNameChanged
DOMCharacterDataModified
DOMElementNameChanged
DOMNodeInserted
DOMNodeInsertedIntoDocument
DOMNodeRemoved
DOMNodeRemovedFromDocument
DOMSubtreeModified
```

这么多事件，各内核各版本浏览器想兼容怕是要天荒地老。

### MutationObserver 的优势
而 Mutation Observer 的优势在于：

* MutationEvents 事件是同步触发，也就是说，DOM 的变动立刻会触发相应的事件；

* Mutation Observer 则是异步触发，DOM 的变动并不会马上触发，而是要等到当前所有 DOM 操作都结束才触发。

* 可以通过配置项，监听目标 DOM 下子元素的变更记录

### MutationObserver 基本使用
使用MutationObserver API主要需要三个步骤：

1. 创建观察者

2. 定义回调函数

3. 定义要观察的目标对象

#### 创建观察者
```js
let observer = new MutationObserver(callback);
```

#### 定义回调函数
上面代码中的回调函数，会在每次 DOM 变动后调用。该回调函数接受两个参数，第一个是变动数组，第二个是观察器实例，下面是一个例子：
```js
function callback (mutations, observer) {
  mutations.forEach(function(mutation) {
    console.log(mutation);
  });
});
```

其中每个 mutation 都对应一个 MutationRecord 对象，记录着 DOM 每次发生变化的变动记录

MutationRecord 对象包含了 DOM 的相关信息，有如下属性：
{% asset_img 6.png %}

#### 定义要观察的目标对象
```js
MutationObserver.observe(dom, options)
```

启动监听，接收两个参数。

* 第一参数：被观察的 DOM 节点。

* 第二参数：配置需要观察的变动项 options。

```js
mutationObserver.observe(content, {
    attributes: true, // Boolean - 观察目标属性的改变
    characterData: true, // Boolean - 观察目标数据的改变(改变前的数据/值)
    childList: true, // Boolean - 观察目标子节点的变化，比如添加或者删除目标子节点，不包括修改子节点以及子节点后代的变化
    subtree: true, // Boolean - 目标以及目标的后代改变都会观察
    attributeOldValue: true, // Boolean - 表示需要记录改变前的目标属性值
    characterDataOldValue: true, // Boolean - 设置了characterDataOldValue可以省略characterData设置
    // attributeFilter: ['src', 'class'] // Array - 观察指定属性
});
```

优先级 ：

1. attributeFilter/attributeOldValue > attributes

2. characterDataOldValue > characterData

3. attributes/characterData/childList（或更高级特定项）至少有一项为 true；

4. 特定项存在, 对应选项可以忽略或必须为true

此外，还有两个方法：

停止观察。调用后不再触发观察器，解除订阅
```js
MutationObserver.disconnect()
```

清除变动记录。即不再处理未处理的变动。该方法返回变动记录的数组，注意，该方法立即生效。
```js
MutationObserver.takeRecords()
```

### 例子1：MutationObserver 监听文本变化
基本使用是：
```js
const target = document.getElementById('target-id')
 
const observer = new MutationObserver(records => {
  // 输入变更记录
})
 
// 开始观察
observer.observe(target, {
  characterData: true
})
```

这里可以有几种处理。

* 聊天的气泡框彩蛋，检测文本中的指定字符串/表情包，触发类似微信聊天的表情落下动画。

* 输入框的热点话题搜索，当输入“#”号时，启动搜索框预检文本或高亮话题。

有个Vue的小型插件就是这么实现的：

> 来自：《vue-hashtag-textarea》

{% asset_img 7.gif %}

### 例子2：色块小游戏脚本
> Hacking the color picker game — MutationObserver

{% asset_img 8.png %}
游戏的逻辑很简单，当中间的色块颜色改变时，在时间限制内于底下的选项选择跟它颜色一样的选项就得分。难的点在于越后面的关卡选项越多，而且选项颜色也越相近，例如：

{% asset_img 9.png %}
其实原理非常简单，就是观察色块的 `backgroundColor`（属性变化attributes)，然后触发点击事件 `e.click()`。

```js
var targetNode = document.querySelector('#kolor-kolor');
var config = { attributes: true };
var callback = function(mutationsList, observer) {
    if (mutationsList[0].type == 'attributes') {
        console.log('attribute change!');
        let ans = document.querySelector('#kolor-kolor').style.backgroundColor;
        document.querySelectorAll('#kolor-options a').forEach( (e) => {
            if (e.style.backgroundColor == ans) {
                e.text = 'Ans!';
                e.click()
            }
        })
    }
};
 
var observer = new MutationObserver(callback);
observer.observe(targetNode, config);
```

{% asset_img 10.png %}

## ResizeObserver：视图观察者
ResizeObserver API是一个新的 JavaScript API，与 IntersectionObserver API非常相似，它们都允许我们去监听某个元素的变化。

### 出现的意义
* 开发过程当中经常遇到的一个问题就是如何监听一个 div 的尺寸变化。

* 但众所周知，为了监听 div 的尺寸变化，都将侦听器附加到 window 中的 resize 事件。

* 但这很容易导致性能问题，因为大量的触发事件。

* 换句话说，使用 window.resize 通常是浪费的，因为它告诉我们每个视窗大小的变化，而不仅仅是当一个元素的大小发生变化。

* **而且 `resize` 事件会在一秒内触发将近 60 次，很容易在改变窗口大小时导致性能问题**

比如说，你要调整一个元素的大小，那就需要在 resize 的回调函数 callback() 中调用 getBoundingClientRect 或 getComputerStyle。不过你要是不小心处理所有的读和写操作，就会导致布局混乱。比如下面这个小示例：
{% asset_img 11.gif %}

### ResizeObserver 的优势
ResizeObserver API 的核心优势有两点：

* 细颗粒度的DOM元素观察，而不是 window

* 没有额外的性能开销，只会在绘制前或布局后触发调用

### ResizeObserver 基本使用
使用 ResizeObserver API 同样也是三个步骤：

1. 创建观察者

2. 定义回调函数

3. 定义要观察的目标对象

#### 创建观察者
```js
let observer = new ResizeObserver(callback);
```

#### 定义回调函数
```js
const callback = entries => {
    entries.forEach(entry => {
 
    })
}
```

每一个 entry 都是一个对象，包含两个属性 contentRect 和 target
{% asset_img 12.png %}

contentRect 都是一些位置信息：
{% asset_img 13.png %}

#### 定义要观察的目标对象
```js
observer.observe(document.body)
```
{% asset_img 14.gif %}

unobserve 方法：取消单节点观察
```js
observer.unobserve(document.body)
```

disconnect 方法：取消所有节点观察
```js
observer.disconnect(document.body)
```

### 例子1：缩放渐变背景
html：
```html
    <div class="box">
        <h3 class="info"></h3>
    </div>
    <div class="box small">
        <h3 class="info"></h3>
    </div>
```

css:
```css
body {
    width: 100vw;
    height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: center;
    padding: 2vw;
    box-sizing: border-box;
}
.box {
    text-align: center;
    height: 20vh;
    border-radius: 8px;
    box-shadow: 0 0 4px rgba(0,0,0,.25);
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 1vw
}
.box h3 {
    color: #fff;
    margin: 0;
    font-size: 5vmin;
    text-shadow: 0 0 10px rgba(0,0,0,0.4);
}
.box.small {
    max-width: 550px;
    margin: 1rem auto;
}
```

js:
```js
const boxes = document.querySelectorAll('.box');
let callbackFired = 0;
const myObserver = new ResizeObserver(entries => {
    for (let entry of entries) {
        callbackFired++
        const infoEl = entry.target.querySelector('.info');
        const width = Math.floor(entry.contentRect.width);
        const height = Math.floor(entry.contentRect.height);
        const angle = Math.floor(width / 360 * 100);
        const gradient = `linear-gradient(${angle}deg, rgba(0,143,104,1) 50%, rgba(250,224,66,1) 50%)`;
        entry.target.style.background = gradient;
        infoEl.innerText = `
            I'm ${width}px and ${height}px tall
            Callback fired: ${callbackFired}
            `;
    }
});
boxes.forEach(box => {
    myObserver.observe(box);
});
```

当你拖动浏览器窗口，改变其大小时，看到的效果如下：
{% asset_img 15.gif %}

### 例子2：响应式Vue 组件
{% asset_img 15.jpg %}

* 假设你要创建一个postItem组件，在大屏上是这样的显示效果
{% asset_img 16.jpg %}

* 在手机上需要这样的效果：
{% asset_img 17.jpg %}

简单的 `@media` 就可以实现:
```css
@media only screen and (max-width: 576px) {
  .post__item {
    flex-direction: column;
  }
 
  .post__image {
    flex: 0 auto;
    height: auto;
  }
}
```
* 但这就很容易出现 当你在超过预期的屏幕（过大）查看页面时，会出现以下的布局：
{% asset_img 18.jpg %}

**@media 查询的最大问题是：**

* 组件响应度取决于屏幕尺寸，而不是响应自身的尺寸。

以下是指令版实现：
{% asset_img 19.jpg %}

使用：
{% asset_img 20.png %}


效果：
{% asset_img 21.jpg %}

这是 vue-responsive-components 库的具体实现代码，还有组件形式的实现，感兴趣的可以去看看。

## PerformanceObserver：性能观察者
这是一个浏览器和 Node.js 里都存在的 API，采用相同 W3C 的 Performance Timeline 规范

在浏览器中，我们可以使用 window 对象取得 window.performance 和 window.PerformanceObserver 。

而在 Node.js 程序中需要 perf_hooks 取得性能对象，如下：
```js
const { PerformanceObserver, performance } = require('perf_hooks')
```

### 出现的意义
首先来看Performance 接口：

* 可以获取到当前页面中与性能相关的信息。它是 High Resolution Time API 的一部分，同时也融合了 Performance Timeline API、Navigation Timing AP、 User Timing API 和 Resource Timing API。

* Performance API 是大家熟悉的一个接口，他记录着几种性能指数的庞大对象集合。
{% asset_img 22.jpg %}

* 若想获得某项页面加载性能记录，就需要调用 performance.getEntries 或者performance.getEntriesByName 来获得。

* 而获得执行效率，也只能通过 performance.now 来计算。
{% asset_img 23.jpg %}

为了解决上述的问题，在 Performance Timeline Level 2 中，除了扩展了 Performance 的基本定义以外，还增加了 PerformanceObserver 接口。

### PerformanceObserver 的优势
PerformanceObserver 是浏览器内部对 Performance 实现的观察者模式，也是现代浏览器支持的几个 Observer 之一。

>  来自：《你了解 Performance Timeline Level 2 吗？》

它解决了以下3点问题：

1. 避免不知道性能事件啥时候会发生，需要重复轮训 timeline 获取记录。

2. 避免产生重复的逻辑去获取不同的性能数据指标

3. 避免其他资源需要操作浏览器性能缓冲区时产生竞态关系。

W3C官网文档鼓励开发人员尽可能使用 PerformanceObserver，而不是通过 Performance 获取性能参数及指标。

### PerformanceObserver 基本使用
使用 PerformanceObserver API 主要需要三个步骤：

1. 创建观察者

2. 定义回调函数事件

3. 定义要观察的目标对象

#### 创建观察者
```js
let observer = new PerformanceObserver(callback); 
```

#### 定义回调函数事件
```js
const callback = (list, observer) => {
    const entries = list.getEntries()
    entries.forEach(entry => {
        console.log("Name: " + entry.name + ", Type: " + entry.entryType + ", Start: " + entry.startTime + ", Duration: " + entry.duration + "\n")
    })
}
```

其中每一个 list 都是一个完整的 PerformanceObserverEntryList 对象：
{% asset_img 24.png %}

包含三个方法 getEntries、 getEntriesByType、 getEntriesByName：
{% asset_img 25.png %}

#### 定义要观察的目标对象
```js
observer.observe({entryTypes: ["entryTypes"]});
```

`observer.observe(...)` 方法接受可以观察到的有效的入口类型。这些输入类型可能属于各种性能 API，比如 User tming 或 Navigation Timing API。有效的entryType 值：
{% asset_img 26.png %}

### 例子1：静态资源监控
> 来自：《资源监控》

```js
function filterTime(a, b) {
  return (a > 0 && b > 0 && (a - b) >= 0) ? (a - b) : undefined;
}
 
let resolvePerformanceTiming = (timing) => {
  let o = {
    initiatorType: timing.initiatorType,
    name: timing.name,
    duration: parseInt(timing.duration),
    redirect: filterTime(timing.redirectEnd, timing.redirectStart), // 重定向
    dns: filterTime(timing.domainLookupEnd, timing.domainLookupStart), // DNS解析
    connect: filterTime(timing.connectEnd, timing.connectStart), // TCP建连
    network: filterTime(timing.connectEnd, timing.startTime), // 网络总耗时
 
    send: filterTime(timing.responseStart, timing.requestStart), // 发送开始到接受第一个返回
    receive: filterTime(timing.responseEnd, timing.responseStart), // 接收总时间
    request: filterTime(timing.responseEnd, timing.requestStart), // 总时间
 
    ttfb: filterTime(timing.responseStart, timing.requestStart), // 首字节时间
  };
 
  return o;
};
 
let resolveEntries = (entries) => entries.map(item => resolvePerformanceTiming(item));
 
let resources = {
  init: (cb) => {
    let performance = window.performance || window.mozPerformance || window.msPerformance || window.webkitPerformance;
    if (!performance || !performance.getEntries) {
      return void 0;
    }
 
    if (window.PerformanceObserver) {
      let observer = new window.PerformanceObserver((list) => {
        try {
          let entries = list.getEntries();
          cb(resolveEntries(entries));
        } catch (e) {
          console.error(e);
        }
      });
      observer.observe({
        entryTypes: ['resource']
      })
    } else {
        window.addEventListener('load', () => {
        let entries = performance.getEntriesByType('resource');
        cb(resolveEntries(entries));
      });
    }
  },
};
```

## 参考文章&总结
参考文章有点多：
1. 资源监控

2. Media Queries Based on Element Width with MutationObserver

3. 以用户为中心的性能指标

4. A Few Functional Uses for Intersection Observer to Know When an Element is in View

5. Getting To Know The MutationObserver API

6. Different Types Of Observers Supported By Modern Browsers

7. THE RESIZE OBSERVER EXPLAINED

8. A Look at the Resize Observer JavaScript API
