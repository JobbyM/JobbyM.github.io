---
title: 一日一练-JS rem布局在webview 中错乱
date: 2018-06-22 13:13:08
tags:
  - 技术
  - 一日一练
  - JS
categories: 技术
---

> 子曰：在实践中学习

今天测试同事说，app 中的h5 页面在Mate Pro 10 中出现错误，不能够占满全屏，只占据了90% 的宽度。
这是一个大bug 啊！

开始进行了排查是否是代码问题？rem 的问题？webview 的问题？最后在网络上检索到是参考文档中的内容。
这里记录了解决方案。

<!--more-->

我们在开发hybrid 应用时，h5 页面的使用rem 进行适配，设计师给出750px 宽度的设计图，在750px 设计图上进行开发。
通常在适配时，都是在`<head>` 标签中加载一段`<script>` 代码：
```js
!(function (doc, win) {
  var docEl = doc.documentElement,
  resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',
  recalc = function () {
    var clientWidth = docEl.clientWidth
    if (!clientWidth) return
    if (clientWidth > 750) {
      clientWidth = 750
    }
    docEl.style.fontSize = 100 * (clientWidth / 750) + 'px'
  }
  recalc()
  if (!doc.addEventListener) return
  win.addEventListener(resizeEvt, recalc, false)
})(document, window)
```
上述代码使得`1rem=100px` 这样就可以开发了。

但是这在有些Android 手机上（这里的Mate 10 Pro），浏览器或 webview 的默认字体是随着系统设置的字体改变的。这样就会导致默认字体大于或小于 16px导致上述bug。
下面代码就可以解决这个问题了：
```js
// 获取系统默认字体大小
// designWidth 设计稿的宽度
// rem2px 设计稿宽度下,1rem的宽度
function adapt(designWidth, rem2px){
  var d = window.document.createElement('p')
  d.style.width = '1rem'
  d.style.display = "none"
  var head = window.document.getElementsByTagName('head')[0]
  head.appendChild(d)
  var defaultFontSize = parseFloat(window.getComputedStyle(d, null).getPropertyValue('width'))
  return defaultFontSize
}
!(function(doc, win, designWidth, rem2px) {
  var docEl = doc.documentElement,
    defaultFontSize = adapt(designWidth, rem2px),
    resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',
    recalc = function() {
      var clientWidth = win.innerWidth ||
        doc.documentElement.clientWidth ||
        doc.body.clientWidth
      if (!clientWidth) return
      if (clientWidth < 750) {
        docEl.style.fontSize = clientWidth / designWidth * rem2px / defaultFontSize * 100 + '%'
      } else {
        docEl.style.fontSize = '625%'
      }
    }
  if (!doc.addEventListener) return
  win.addEventListener(resizeEvt, recalc, false)
  doc.addEventListener('DOMContentLoaded', recalc, false)
})(document, window, 750, 100)
```

[猫眼](http://piaofang.maoyan.com)的`rem` 适配方法
```js
/**
 * 使用 rem 布局不要使用 text-size-adjust 改变字体效果，
 * 在 <meta name="viewport"> 之后使用该代码
 * @param {Number} designCSSWidth       设计基准CSS宽度，设备宽度 / dpr
 * @param {Number} baseFontSize         基准字体大小，当前 innerWidth 和 designCSSWidth 宽度相同时，html 的 font-size px 值
 * @param {Number} maxScale             最大的缩小放大倍数
 */
function rem(designCSSWidth, baseFontSize, maxScale){
    designCSSWidth = designCSSWidth || 760;
    baseFontSize = baseFontSize || 100;
    maxScale = maxScale || 1.5;

    var w = window;
    var d = document;
    var on = 'addEventListener';
    var html = d.documentElement;
    var maxSize = maxScale * baseFontSize;
    var remStyle = d.getElementById('rem-style');
    function setRem() {
        var isVertical = w.innerWidth < w.innerHeight;
        var width = w.innerWidth;
        if (!isVertical) {
            width = 375;
            remStyle.innerText = 'body { max-width: ' + width + 'px}';
        } else {
            remStyle.innerText = '';
        }
        var size = baseFontSize * width / designCSSWidth;
        html.style.fontSize = Math.min(size, maxSize) + 'px';
    }

    var isAnd = w.navigator.userAgent.match(/Android/);
    var raf = w.requestAnimationFrame || w.webkitRequestAnimationFrame;
    var limit = 10;
    function setRemFallback() {
        // 经测试不能保证第一次就拿到正确 innerWidth，所以设置一个延时（即次数限制）。
        limit--;
        if (limit && w.innerWidth > 500) {
            raf && raf(setRemFallback)
        } else {
            setRem();
            d.documentElement.style.display = 'block';
        }
    }
    if (w.innerWidth > 500 && isAnd) {
        d.documentElement.style.display = 'none';
        /**
         * 部分机型在 WebView 内需要 load 之后才能获取正确 innerWidth。判断标准为其大于 500 的时候。
         * 先隐藏 HTML，拿到正确的 innerWidth 再显示。有问题的机器包括魅族 M1
         */
        if (!raf) {
            w[on]('load', setRemFallback, false);
        } else {
            raf(setRemFallback);
        }
    }
    w[on]('resize', setRem, false);
    setRem();
}
rem(375, 50);
```

## 参考文档
1.[rem布局在webview中页面错乱](https://edu.aliyun.com/a/65330)
