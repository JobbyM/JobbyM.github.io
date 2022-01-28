---
title: 一日一练-JS 如何上报前端性能数据
date: 2022-01-28 09:17:22
tags:
  - 技术
  - 一日一练
  - JS
  - 性能
  - 转载
categories: 技术
---

本文转载自[在单页应用中，如何优雅的上报前端性能数据](https://github.com/forthealllight/blog/issues/38)，有部分删节，看原文请到原地址。

最近在做一个较为通用的前端性能监控平台，区别于前端异常监控，前端的性能监控主要需要上报和展示的是前端的性能数据，包括首页渲染时间、每个页面的白屏时间、每个页面所有资源的加载时间以及每一个页面中所以请求的响应时间等等。

本文的介绍的是如何设计一个通用的 jssdk,可以以较小的侵入性，自动上报前端的性能数据。主要采用的是 `Performance API` 以及 `sendBeacon` 方法等等。主要参考的是 `google analytics` 以及阿里云前端性能监控平台的实践。

在我的项目中使用 `nestjs` 作为后端框架，`nestjs` 是基于 `express` 的一款完美支持 `typescript`，类 `java spring` 的 `node` 后端框架。本文主要侧重与如何上报性能数据，后端处理逻辑比较简单，不会具体介绍，因此不需要了解如何使用 `nestjs`。本文的主要内容包含了：

* 根据 `Performance API` 获取前端性能数据
* 何时应该上报性能数据
* 如何上报性能数据

<!--more-->

## 一、根据 Performance API 获取前端性能数据
本文上报的前端性能数据包含两部分，一是通过 `Performance API` 获得的性能数据，二是自定义的在每个页面应该上报的数据。

首先来看通过 `Performance API` 所获取的数据,该数据也包含了两个部分，当前页面的性能相关数据以及当前页面资源加载和异步请求的相关数据。

### (1)、Performance API 所提供的性能数据
`window.performance.timing` 会返回一个对象，该对象包含了各种与页面渲染所相关的数据。本文不会具体去介绍该对象，只给出根据该对象计算相关性能数据的方法：
```js
  let times = {};
  let t = window.performance.timing;
  
  //重定向时间
  times.redirectTime = t.redirectEnd - t.redirectStart;
  
  //dns查询耗时
  times.dnsTime = t.domainLookupEnd - t.domainLookupStart;
  
  //TTFB 读取页面第一个字节的时间
  times.ttfbTime = t.responseStart - t.navigationStart;
  
  //DNS 缓存时间
  times.appcacheTime = t.domainLookupStart - t.fetchStart;
  
  //卸载页面的时间
  times.unloadTime = t.unloadEventEnd - t.unloadEventStart;
  
  //tcp连接耗时
  times.tcpTime = t.connectEnd - t.connectStart;
  
  //request请求耗时
  times.reqTime = t.responseEnd - t.responseStart;
  
  //解析dom树耗时
  times.analysisTime = t.domComplete - t.domInteractive;
  
  //白屏时间
  times.blankTime = t.domLoading - t.fetchStart;
  
  //domReadyTime
  times.domReadyTime = t.domContentLoadedEventEnd - t.fetchStart;
```

在上面的 `times` 对象中就包含了性能相关的属性，根据 `performance.timing` 中的相关属性计算就可以得到结果。在这里我们认为 `domReadyTime` 就是首屏加载的时间，此外也可以自定义的方法上报首屏的时间：

比如有些场景可以认为是 `dom` 增量最大的点为首屏渲染完成的时间，也有一些场景可以定义可见的 `dom` 在增量最大处为首屏渲染完成的时间。

### (2)、Performance API 所提供的资源加载和请求数据
可以通过 `window.performance.getEntries()` 来获取资源的加载和请求相关的数据。每一个页面中，需要去加载很多资源比如 `js、css` 等等，同时在页面中还会存在一些异步请求。通过 `window.performance.getEntries()` 可以获得这些资源加载和异步请求所相关的数据。我们可以通过如下的方式来获取加载和异步请求的数据：
```js
  let entryTimesList = [];
  let entryList = window.performance.getEntries();
  entryList.forEach((item,index)=>{
  
     let templeObj = {};
     
     let usefulType = ['navigation','script','css','fetch','xmlhttprequest','link','img'];
     if(usefulType.indexOf(item.initiatorType)>-1){
       templeObj.name = item.name;
       
       templeObj.nextHopProtocol = item.nextHopProtocol;
      
       //dns查询耗时
       templeObj.dnsTime = item.domainLookupEnd - item.domainLookupStart;

       //tcp链接耗时
       templeObj.tcpTime = item.connectEnd - item.connectStart;
       
       //请求时间
       templeObj.reqTime = item.responseEnd - item.responseStart;

       //重定向时间
       templeObj.redirectTime = item.redirectEnd - item.redirectStart;

       entryTimesList.push(templeObj);
     }
  });
```

我们通过 `window.performance.getEntries()` 获得一个带有资源加载和异步请求相关数据的数组，然后根据数组中每一个元素的 `initiatorType` 属性来过滤出属性为 `['navigation','script','css','fetch','xmlhttprequest','link','img']` 之一的元素数据。

### (3)、注意点
* 通过 `window.performance.timing` 所获的的页面渲染所相关的数据，在单页应用中改变了 `url` 但不刷新页面的情况下是不会更新的。因此如果仅仅通过该 `api` 是无法获得每一个子路由所对应的页面渲染的时间。如果需要上报切换路由情况下每一个子页面重新 `render` 的时间，需要自定义上报。

* 通过 `window.performance.getEntries()` 所获取的资源加载和异步请求所相关的数据，在页面切换路由的时候会重新的计算，可以实现自动的上报。

## 二、何时上报性能数据
接着来确定应该何时上报性能数据，因为要处理 `pv`(访问量)和 `uv`(独立用户访问量)，一般认为一次上报就是一次访问，那么何时上报性能数据呢。在我的系统中选择在一下场景下进行一次前端性能数据的上报：

* 页面加载和重新刷新
* 页面切换路由
* 页面所在的 `tab` 标签重新变得可见

针对上述的 3 种场景，特别是切换路由的情况，如果切换路由是通过改变 `hash` 值来实现的，那么只需要监听 `hashchange` 事件，如果是通过 `html5` 的 `history api` 来改变 `url` 的，那么需要重新定义 `pushstate` 和 `replacestate` 事件。具体的做法可以看我的上一篇文章：[在单页应用中，如何优雅的监听url的变化](https://github.com/forthealllight/blog/issues/37)。

直接给出 `history` 实现路由场景下监听 `url` 改变的方案：
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

然后我们就可以根据上述场景，分别监听相应的事件，从而实现前端性能数据的上报：
```js
addEvent(window,'load',function(e){
    ...deal with something
});
//监控history基础上实现的单页路由中url的变化
addEvent(window,'replaceState', function(e) {
    ...deal with something
});
addEvent(window,'pushState', function(e) {
    ...deal with something
});
//通过hash切换来实现路由的场景
addEvent(window,'hashchange',function(e){
   ...deal with something
});
addEvent('document','visibilitychang'，function(e){
   ...deal with something
})
```

`addEvent` 是一个兼容 IE 和标准 DOM 事件流模型的事件。

## 三、如何上报性能数据

那么如何上报性能数据呢，我们第一反应就是通过 `ajax` 请求的形式来上报前端性能数据。这种方法有一些缺陷，比如必须对跨域做特殊处理以及如果页面销毁后，相应的 `ajax` 方法并不一定发送成功等问题。

其中跨域的问题比较好处理，最难解决的问题是第二点：

**就是如果页面销毁，那么对应的 `ajax` 方法并不一定能成功发送。**

我们可以根据 `google analytics(GA)` 中的方法，根据浏览器的兼容性以及 `url` 的长度，来采用不同的方法上报性能数据，主要原理是：

通过动态创建 `img` 标签的方式，在 `img.src` 中拼接 `url` 的方式发送请求，不存在跨域限制。如果 `url` 太长，则才用 `sendBeacon` 的方式发送请求，如果 `sendBeacon` 方法不兼容，则发送 `ajax post` 同步请求

### (1)、sendBeacon方法
解决在文档卸载或者页面关闭后无法完成异步 `ajax` 请求的问题，很多情况下我们会把异步变成同步。在页面卸载的 `unload` 或者 `beforeunload` 事件中执行同步方法调用。

但是同步方法调用存在一个问题，就是会推迟 A 页面切换进入 B 页面的时间。而 `sendBeacon` 方法解决了该问题，简单来说：

**`sendBeacon` 方法在页面销毁期，可以异步的发送数据，因此不会造成类似同步 `ajax` 请求那样的阻塞问题,也不会影响下一个页面的渲染**

`sendBeacon` 的调用方式为：
```js
navigator.sendBeacon(url [, data]);
```

`data` 可以为： `ArrayBufferView`, `Blob`, `DOMString`, 或者 `FormData`

为了发送参数，我们一般 `data` 制定为 `Blob` 的形式。此外还要注意的是，在 `sendBeacon` 的请求头 `header` 中，不支持 `Content-Type` 为 `"application/json; charset=utf-8"`。

在 `sendBeacon` 的 `header` 中，只支持一下 3 种形式的 `Content—Type`：

* `application/x-www-form-urlencoded`
* `multipart/form-data`
* `text/plain`

一般制定为 `application/x-www-form-urlencoded`，完整的通过 `sendBeacon` 来发送请求的例子如下：
```js
function sendBeacon(url,data){
  //判断支不支持navigator.sendBeacon
  let headers = {
    type: 'application/x-www-form-urlencoded'
  };
  let blob = new Blob([JSON.stringify(data)], headers);
  navigator.sendBeacon(url,blob);
}
```

后端如何处理 `sendBeacon` 请求呢，`sendBeacon` 在的请求头中发送的是一个类似与 `POST` 的请求，因此可以类似于处理 `post` 一样来处理 `sendBeacon` 请求。

一般我们约定 `ajax` 请求的 `content—type` 为：`"application/json; charset=utf-8"`，而 `sendBeacon` 请求的`content-type` 为：`"application/x-www-form-urlencoded"`，这样在后端处理中，就可以区别是正常的 `ajax post` 请求还是 `sendBeacon` 请求。

此外，在处理请求的时候如果存在跨域问题，通过 `cors` 跨域的方式来处理，后端需要配置：`allow-control-allow-origin` 等，可以通过 `express` 的 `cors` 包，来简化配置：
```js
async function bootstrap() {
  const app = await NestFactory.create(ApplicationModule,instance);
  app.use(cors());

  await app.listen(3000)
}
bootstrap();
```

### (2)动态创建 img 标签的形式
通过动态创建 `img` 标签的形式，指定 `src` 属性所指定的 `url` 来发送请求，首先不受跨域的限制，其次 `img` 标签动态插入，会延迟页面的卸载保证图片的插入，因此可以保证在页面的销毁期，请求可以发生。

下面是一个动态创建 `img` 标签的例子：
```js
function imgReport(url, data) {
   if (!url || !data) {
       return;
   }
   let image = document.createElement('img');
   let items = [];
   items = JSON.Parse(data);
   let name = 'img_' + (+new Date());
   image.onload = image.onerror = function () {
      
   };
   let newUrl = url + (url.indexOf('?') < 0 ? '?' : '&') + items.join('&');

   image.src = newUrl;
}
```

此外，我们在动态创建 `img` 标签发送请求的时候，请求的是一张图片，在后端处理的时候，要在末尾将这个图片返回，这样前端的 `image.onload` 方法才会被触发。我们以请求的地址为：`localhost:8080/1.jpg` 为例，后端的处理逻辑为：
```js
@Controller('1.jpg')
export class AppUploadController {
  constructor(private readonly appService: AppService) {}
  @Get()
  getUpload(@Req() req,@Res() res): void {
  
    ...deal with some thing
    res.sendFile(join(__dirname, '..', 'public/1.jpg'))
  }
}
```

在 `get` 请求的处理中，我们通过 `res.sendFile(join(__dirname, '..', 'public/1.jpg'))` 将图片返回后，这样前端的 `image` 的 `onload` 方法才会被调用。

### (3)同步ajax post请求
动态创建 `img` 标签的方法，拼接 `url` 的时候存在一定的问题，因为浏览器对 `url` 的长度是有限制的。而 `sendBeacon` 方法兼容性不是很好，最后兜底的处理方式就是发送同步的 `ajax` 请求，同步的 `ajax` 请求前面说过，会在页面销毁期之前执行，虽然会有一定程度的阻塞下一个页面的渲染。
```js
function xmlLoadData(url,data) {
  var client = new XMLHttpRequest();
  client.open("POST", url,false);
  client.setRequestHeader("Content-Type", "application/json; charset=utf-8");
  client.send(JSON.stringify(data));
}
```

### (4)综合解决方案
一般首先拼接携带参数的完整的 `url`，判断 `url` 的长度，如果 `url` 的长度小于浏览器允许的最大长度内，那么通过动态创建 `img` 标签的形式来发送前端性能数据，如果 `url` 太长，则判断浏览器是否支持 `sendBeacon` 方法，如果支持，则通过`sendBeacon` 方法来发送请求，否则发送同步的 `ajax` 请求。
```js
function dealWithUrl(url,appId){
    let times = performanceInfo(appId);
    let items = decoupling(times);
    let urlLength = (url + (url.indexOf('?') < 0 ? '?' : '&') + items.join('&')).length;
    if(urlLength<2083){
    imgReport(url,times);
    }else if(navigator.sendBeacon){
    sendBeacon(url,times);
    }else{
    xmlLoadData(url,times);
    }
}
```