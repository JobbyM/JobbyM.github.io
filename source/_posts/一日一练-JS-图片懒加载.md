---
title: 一日一练-JS 图片懒加载
date: 2021-07-02 10:34:54
tags:
  - 技术
  - 懒加载
  - 转载
categories: 技术
---

> 子曰：万里之行，始于足下。

本文是根据知乎上的这篇文章 [原生 js 实现图片懒加载](https://zhuanlan.zhihu.com/p/55311726) 实现的

<!--more-->

## 一.什么是懒加载？
懒加载突出一个“懒”字，懒就是拖延迟的意思，所以“懒加载”说白了就是延迟加载，比如我们加载一个页面，这个页面很长很长，长到我们的浏览器可视区域装不下，那么懒加载就是优先加载可视区域的内容，其他部分等进入了可视区域在加载。

## 二.为什么要懒加载？
懒加载是一种网页性能优化的方式，它能极大的提升用户体验。就比如说图片，图片一直是影响网页性能的主要元凶，现在一张图片超过几兆已经是很经常的事了。如果每次进入页面就请求所有的图片资源，那么可能等图片加载出来用户也早就走了。所以，我们需要懒加载，进入页面的时候，只请求可视区域的图片资源。

总结出来就两个点：

1.全部加载的话会影响用户体验

2.浪费用户的流量，有些用户并不像全部看完，全部加载会耗费大量流量。

## 三.懒加载的实现原理？
由于网页中占用资源较多的一般是图片，所以我们一般实施懒加载都是对图片资源而言的，所以这里的实现原理主要是针对图片。

大家都知道，一张图片就是一个 `<img>` 标签，而图片的来源主要是 `src` 属性。浏览器是否发起亲求就是根据是否有 `src` 属性决定的。

既然这样，那么我们就要对 `<img>` 标签的 `src` 属性下手了，在没进入可视区域的时候，我们先不给这个 `<img>` 标签赋 `src` 属性，这样岂不是浏览器就不会发送请求了。

总结：我们知道了从 `<img>` 标签下手，那么更重要的就是可视区域的判断了，这就是整篇文章的核心之处了。

## 四.实现步骤及Demo
### 1.先介绍几个和懒加载相关的API
```js
document.documentElement.clientHeight // 获取屏幕可视区域的高度
```

MDN 上的解释：
> 这个属性是只读属性，对于没有定义 CSS 或者内联布局盒子的元素为 0，否则，它是元素内部的高度(单位像素)，包含内边距，但不包括水平滚动条、边框和外边距。

直观的图解：
{% asset_img 1.png %}

```js
element.offsetTop // 获取元素相对于文档顶部的高度
```

MDN 上的解释：
> HTMLElement.offsetTop 为只读属性，它返回当前元素相对于其 offsetParent 元素的顶部内边距的距离。


```js
element.offsetParent 
```

MDN 上的解释：
> HTMLElement.offsetParent 是一个只读属性，返回一个指向最近的（指包含层级上的最近）包含该元素的定位元素或者最近的 table,td,th,body元素。当元素的 style.display 设置为 "none" 时，offsetParent 返回 null。offsetParent 很有用，因为 offsetTop 和 offsetLeft 都是相对于其内边距边界的。


```js
document.documentElement.scrollTop // 获取浏览器窗口顶部与文档顶部之间的距离，也就是滚动条滚动的距离
```

MDN 上解释:
> Element.scrollTop 属性可以获取或设置一个元素的内容垂直滚动的像素数。
一个元素的 scrollTop 值是这个元素的内容顶部（卷起来的）到它的视口可见内容（的顶部）的距离的度量。当一个元素的内容没有产生垂直方向的滚动条，那么它的 scrollTop 值为0。

通过上面三个API，我们获得了三个值：可视区高度、元素相对于其父元素容器顶部的距离、浏览器窗口顶部与容器元素顶部的距离也就是滚动条滚动的高度。

可能到这里还有一些人不知道怎么实现，我们还是用图来展示一下：
{% asset_img 2.jpg %}

大家看了这张图，应该一下就能明白过来了。所以我们就得出了一个判断公式：

如果：`offsetTop-scroolTop<clientHeight`，则图片进入了可视区内，则被请求。

### 2.代码实现
下面的代码就是根据以上公式实现的：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>图片懒加载</title>
    <style>
        img {
            display: block;
            width: 100%;
            height: 300px;
            margin-bottom: 20px;
        }
    </style>
</head>
<body>
    <img data-src="./images/1.jpg" alt="">
    <img data-src="./images/2.jpg" alt="">
    <img data-src="./images/3.jpg" alt="">
    <img data-src="./images/4.jpg" alt="">
    <img data-src="./images/5.jpg" alt="">
    <img data-src="./images/6.jpg" alt="">
    <img data-src="./images/7.jpg" alt="">
    <img data-src="./images/8.jpg" alt="">
    <img data-src="./images/9.jpg" alt="">
    <img data-src="./images/10.jpg" alt="">
    <img data-src="./images/11.jpg" alt="">
    <img data-src="./images/12.jpg" alt="">
</body>
<script>
    var imgs = document.querySelectorAll('img')

    // offsetTop 是元素与 offsetParent 的距离，循环获取直到页面的顶部
    function getTop(e) {
        var T = e.offsetTop
        while(e = e.offsetParent) {
            T += e.offsetTop
        }
        return T
    }

    function lazyLoad(imgs) {
        // 获取可视区域高度
        var H = document.documentElement.clientHeight

        var S = document.documentElement.scrollTop || document.body.scrollTop
        
        for (var i = 0; i < imgs.length; i ++) {
            if (H + S > getTop(imgs[i]) && !imgs[i].getAttribute('src')) {
                imgs[i].src = imgs[i].getAttribute('data-src')
            }
        }
    }

    //  onscroll() 在滚动条滚动的时候触发
    window.onload = window.onscroll = function() {
        lazyLoad(imgs)
    }
</script>
</html>
```

## 五.更加方便快捷的实现方式
### 1.了解一个 API
这种实现方式我们只需要了解一个 API 就行了：
```js
getBoundingClientRect() // 获取元素的大小及位置
```

MDN 上的解释：
> Element.getBoundingClientRect() 方法返回元素的大小及其相对于视口的位置。
如果是标准盒子模型，元素的尺寸等于 `width/height + padding + border-width` 的总和。如果 `box-sizing: border-box`，元素的的尺寸等于 width/height。
返回值是一个 DOMRect 对象，这个对象是由该元素的 getClientRects() 方法返回的一组矩形的集合，就是该元素的 CSS 边框大小。返回的结果是包含完整元素的最小矩形，并且拥有left, top, right, bottom, x, y, width, 和 height这几个以像素为单位的只读属性用于描述整个边框。除了width 和 height 以外的属性是相对于视图窗口的左上角来计算的。

{% asset_img 4.png %}

从这张图我们可以看出，元素是相对于左上角，而不是什么边距。

### 2.实现方式
通过上面的实验我们都知道，懒加载的一个重点就是要知道什么时候图片是进入了可视区内，那么就上面这张图而言，我们有什么方法判断图片进入了可视区呢。

其实很简单：

我们先获取图片到可视区顶部的距离,并获取到可视区的高度：
```js
var bound = el.getBoundingClientRect();
var clientHeight = window.innerHeight;//这个和前面获取可视区高度的效果一样，只是兼容性问题
```

然后我们继续思考，当我们滚动条向下滚动的时候，bound.top 值会变得越来越小，也就是图片到可视区顶部的距离也越来越小，所以当 `bound.top == clientHeight` 时，说明图片马上就要进入可视区了，只要我们在滚动，图片就会进入可视区，那么就需要请求资源了。也就是说，在 `bound.top<=clientHeight` 时，图片是在可视区域内的。

经过上面的思考，我们大致明白了如何实现，那么就来编写我们的代码了吧：

只需要把我们的 js 代码换成如下即可：
```js
    var imgs = document.querySelectorAll('img')

    // 用来判断 bound.top <= clientHeight 的函数
    function isIn(el) {
        var bound = el.getBoundingClientRect()
        var clientHeight = window.innerHeight
        return bound.top <= clientHeight
    }

    // 检查图片是否在可视区内，如果不在，则加载
    function check() {
        Array.from(imgs).forEach(function(el){
            if (isIn(el)) {
                loadImg(el)
            }
        })
    }

    function loadImg(el) {
        if (!el.src) {
            var source = el.dataset.src
            el.src = source
        }
    }
    // onscroll() 在滚动条滚动的时候触发
    window.onload = window.onscroll = function () {
        check()
    }
```

## 六.更更加方便快捷的实现方式
### 1.了解一个 API
这种实现方式我们只需要了解一个 API 就行了：
```js
IntersectionObserver 
```

IntersectionObserver 接口，提供了一种异步观察目标元素与其祖先元素或顶级文档视窗( viewport )交叉状态的方法，祖先元素与视窗( viewport )被称为根( root )

使用 IntersectionObserver API 主要需要三个步骤：
1. 创建观察者

2. 定义回调事件

3. 定义要观察的目标对象

### 2.实现方式
```js
let observer = new IntersectionObserver(
    (entries, observer) => {
        entries.forEach(entry => {
            // 替换属性
            if (entry.isIntersecting) {
                entry.target.src = entry.target.dataset.src
                observer.unobserve(entry.target)
            }
        })
    },
    { rootMargin: "0px 0px -20px 0px" }
)

document.querySelectorAll('img').forEach(img => { observer.observe(img) })
```

## 总结
懒加载其实就两个重点，一个是元素到各个边距的距离，二个就是判断元素是否在可视区域内。