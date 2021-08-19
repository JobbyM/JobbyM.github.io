---
title: 一日一练-JS 节流和防抖
date: 2021-08-18 23:17:31
tags:
  - 技术
  - 一日一练
  - JS
  - 节流
  - 防抖
  - throttle
  - debounce
categories: 技术
---


本文转载自[听说前端面试手写”节流防抖“你不会？用动画带你秒懂！](https://mp.weixin.qq.com/s/6tjUuaePQvdHKeAGuM-VOA)，有部分删节，看原文请到原地址。

> 子曰：一日一练-JS 节流和防抖

节流和防抖都是用来控制某些函数的调用频率。举个例子，在窗口 resize 的时候，由于可视区变大，我们可能需要向服务器请求更多内容来填充可视区。但可视区变大的过程中，resize事件会被触发多次...每次触发都去请求一次的话没有必要...这时就需要节流防抖来做控制

<!--more-->

```js
    function throttle(func, delay) {
        var last = 0
        return function () {
            var now = Date.now()
            if (now >= delay + last) {
                func.apply(this, arguments)
                last = now
            } else {
                console.log('距离上次调用的时间差不满足要求哦')
            }
        }
    }
    function resize(){
        console.log('窗口大小改变了')
    }
    // window.addEventListener('resize', resize)
    window.addEventListener('resize', throttle(resize, 500))
```

## 节流（throttle）
当 resize 事件被触发后，指定时间内不允许再次触发。

### 1. 时间戳版
```js
function throttle(func, delay) {
    var last = 0
    return function(){
        var now = Date.now()
        if (now >= delay + last) {
            func.apply(this, arguments)
            last = now
        } else {
            console.log('距离上次调用的时间差不满足要求哦')
        }
    }
}
```

### 2. 定时器版
```js
function throttle(func, delay) {
    var timer = null
    return function(){
        if(!timer){
            func.apply(this, arguments)
            timer = setTimeout(()=>{
                timer = null
            }, delay)
        } else {
            console.log('上一个定时器尚未完成')
        }
    }
}
```

无论哪种写法，节流的意思就是函数在一段时间内的多次调用，仅第一次有效。

所以 节流 就像一个看门大爷，每一段时间他只会放一个人进去
{% asset_img 1.gif %}

## 防抖（debounce）
防抖和节流不同的地方在于，函数在一段时间内的多次调用，仅使得最后一次调用有效。
```js
function debounce(func, delay) {
    var timeout
    return function(){
        clearTimeout(timeout)
        timeout = setTimeout(() =>{
            func.apply(this, arguments)
        }, delay)
    }
}
```

所以防抖就像是PK赛里的待定区，下一个待定的人会把上一个待定的人踢出局
{% asset_img 2.gif %}

