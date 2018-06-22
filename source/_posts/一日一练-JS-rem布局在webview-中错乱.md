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

## 参考文档
1.[rem布局在webview中页面错乱](https://edu.aliyun.com/a/65330)
