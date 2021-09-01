---
title: 一日一练-浏览器 【转载】Chrome Devtool - Performance
date: 2021-08-31 18:09:53
tags:
  - 技术
  - 一日一练
  - 浏览器
  - Performance
  - 转载
categories: 技术
---

本文转载自[Chrome Devtool — Performance](https://blog.csdn.net/qq_38128179/article/details/104491149)，有部分删减，看原文请到原地址。

## 前言
Performance 一个在前端开发领域中，无法被忽视的存在。使用 Chrome DevTools 的 performance 面板可以记录和分析页面在运行时的所有活动。本文将详细介绍如何使用performance 面板解决性能瓶颈。

<!--more-->

## 一、Performance 工具优点
* 可视化图形界面
* 每毫秒做的事情
* 文件的执行加载的顺序
* 每毫秒界面展示的效果
* 每个方法执行的顺序和时间（由下至上）
* 倒置的事件火焰图（由下至上）
* 数据总结

## 二、熟悉 Performance 面板
{% asset_img 1.png %}

## 三、工具栏

【3.1】录制: 点击 Record （按Ctrl+E），这时候 Devtools 就开始录制各种性能指标。记录时，Record 按钮会变成红色。然后按 Record 按钮或再次键入键盘快捷键停止记录；

【3.2】刷新: 刷新页面分析；

【3.3】清除: 清除页面分析结果；

【3.4】上下箭头: 用来上传和下载每一次性能检测报告；

【3.5】Screendshots:  显示屏幕快照，是用来查看在每个时间段界面的变化；

【3.6】Memory: 存储调用栈的大小，在不同时间段的不同大小；

【3.7】Disable Javascript samples:  禁用 JavaScript 调用栈；

【3.8】Enable advanced paint instrumentation(slow):  记录渲染事件的细节；

【3.9】Network: 模拟不同的网络环境；

【3.10】CPU: 模拟不同的CPU运行速度；


## 四、overview(页面性能高级汇总)
这里最主要是整体的界面渲染的时候，每个时间段执行的事件顺序，我们就能知道我们每个时间段（精确到毫秒）都做了什么，当鼠标放上去的时候，我们还可以大图的形式去查看我们每个时间段界面的渲染情况：

【4.1】FPS: 全称 Frames Per Second，表示每秒传输帧数，是速度单位，用来分析动画的一个主要性能指标。如果能够达到 >=60fps(帧)/s 的刷新频率，就可以避免出现卡顿。能保持在60的FPS的话，那么用户体验就是不错的。

为什么是 60fps?

我们的目标是保证页面要有高于每秒 60fps(帧)的刷新频率，这和目前大多数显示器的刷新率相吻合(60Hz)。如果网页动画能够做到每秒 60 帧，就会跟显示器同步刷新，达到最佳的视觉效果。这意味着，一秒之内进行 60 次重新渲染，每次重新渲染的时间不能超过 `1/60=0.01666s`（秒）， `0.01666s*1000=16.66ms`（毫秒）。

不同帧的体验：
* 帧率能够达到 50 ～ 60 FPS 的动画将会相当流畅，让人倍感舒适；
* 帧率在 30 ～ 50 FPS 之间的动画，因各人敏感程度不同，舒适度因人而异；
* 帧率在 30 FPS 以下的动画，让人感觉到明显的卡顿和不适感；
* 帧率波动很大的动画，亦会使人感觉到卡顿。

如下图所示，绿色的长条越高，说明 FPS 越高，用户体验越好。 如果你发现了一个红色的长条，那么就说明这些帧存在严重问题，有可能导致非常差的用户体验。
{% asset_img 2.png %}

【4.2】CPU: CPU 资源。CPU图表中的各种颜色代表着在这个时间段内，CPU在各种处理上所花费的时间。如果你看到了某个处理占用了大量的时间，那么这可能就是一个可以找到性能瓶颈的线索。

下图中颜色分别为(与Summary面板里的颜色是相互对应的)：

* 蓝色(Loading): 表示网络通信和 HTML 解析时间
* 黄色(Scripting): 表示 JavaScript 执行时间
* 紫色(Rendering): 表示样式计算和布局（重排）时间
* 绿色(Painting): 表示重绘时间
* 灰色(other): 表示其它事件花费的时间
* 白色(Idle): 表示空闲时间

{% asset_img 3.png %}

【4.3】NET: 每条彩色横杠表示一种资源。横杠越长，检索资源所需的时间越长。 每个横杠的浅色部分表示等待时间（从请求资源到第一个字节下载完成的时间）。
{% asset_img 4.png %}

【4.4】HEAP: JavaScrip 执行的时间分布。
{% asset_img 5.png %}

## 五、火焰图(CPU 堆叠追踪的可视化)
{% asset_img 6.png %}

【5.1】Network: 表示每个服务器资源的加载情况，什么时间加载了什么资源，通过这里，我们更直观的可以知道，资源是并行加载的

【5.2】Frames: 表示每幅帧的运行情况

【5.3】Timings: 上图中有 4 条虚线，分别表示如下：

* DCL(DOMContentLoaded): 表示 HTML 文档加载完成事件。当初始 HTML 文档完全加载并解析之后触发，无需等待样式、图片、子 frame 结束。作为明显的对比，load 事件是当个页面完全被加载时才触发
* FP(First Paint): 首屏绘制，页面刚开始渲染的时间
* FCP(First Contentful Paint): 首屏内容绘制，首次绘制任何文本，图像，非空白canvas 或 SVG 的时间点
* FMP(First Meaningful Paint): 首屏有意义的内容绘制，这个“有意义”没有权威的规定，本质上是通过一种算法来猜测某个时间点可能是 FMP。有的理解为是最大元素绘制的时间，即同LCP(Largest Contentful Paint）
* L(Onload): 页面所有资源加载完成事件。
* LCP(Largest Contentful Paint): 最大内容绘制，页面上尺寸最大的元素绘制时间。

其中 FP、FCP、FMP 是同一条虚线，三者时间不一致。比如首次渲染过后，有可能出现 JS 阻塞，这种情况下 FCP 就会大于 FP

【5.4】Main: 表示主线程

主要负责：

* Javascript 的计算与执行
* CSS 样式计算
* Layout 布局计算
* 将页面元素绘制成位图（paint），也就是光栅化（Raster）
* 将位图给合成线程

【5.5】Raster: 光栅化（处理光栅图，即位图）
【5.6】GPU: 表示 GPU 占用情况
【5.7】Chrome_childIOThread: 子线程
【5.8】Compositor: 合成线程

主要负责：

* 将位图（GraphicsLayer 层）以纹理（texture）的形式上传给 GPU
* 计算页面的可见部分和即将可见部分（滚动）
* CSS 动画处理
* 通知 GPU 绘制位图到屏幕上

【5.9】Memory: 上面有提到 Memory 选项，在勾选后，就会显示该事件折线图，通过该图，可以看出我们在不同的时间段，不同事件的执行情况

* JS Heap: 表示 JS 占用的内存大小。
* Documents: 表示文档数。
* Nodes: 表示 Node 节点数
* Listeners: 表示监听数。
* GPU Memory: 表示 GPU 占用数

4 条折线图是以上 4 个指标（没有 GPU 消耗）对应的时间消耗的内存大小与节点数量。若将某项指标前面的勾选去掉则不会出现对应的折线。注意这个折线图只有在点击 Main 主线程的时候才会有，选择其他的指标时折线图区域时空白。

## 六、统计汇总(以图表的形式汇总数据)
{% asset_img 7.png %}

【6.1】Summary: 表示各指标时间占用统计报表

* Loading: 加载时间
* Scripting: js计算时间
* Rendering: 渲染时间
* Painting: 绘制时间
* Other: 其他时间
* Idle: 浏览器闲置时间

【6.2】Bottom-Up: 表示事件时长排序列表（倒序）

这里和 Main 里面看见的，其实是一个对应着的关系，从这里，我们可以看见所有的事件列表，还有每个事件的 Self Time(自己调用的时间） 、Total Time (总调用时间，包括子项调用时间） 、Activity (行为，包括调用该事件的位置）

{% asset_img 8.png %}

【6.3】Call Tree: 表示事件调用顺序列表

其实这里和 Bottom-Up 部分是一样的，就不做太多的说明了
{% asset_img 9.png %}

【6.4】Event Log: 表示事件发生的顺序列表

这里比前面的 Bottom-Up 和 Call Tree 相比，多了一个 Start Time 属性，这个属性其实就是开始的时间，从什么时间开始执行的什么事件

{% asset_img 10.png %}

