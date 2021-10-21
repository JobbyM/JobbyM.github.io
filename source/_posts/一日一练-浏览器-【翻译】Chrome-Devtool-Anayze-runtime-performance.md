---
title: 一日一练-浏览器 【翻译】Chrome Devtool - Anayze runtime performance
date: 2021-09-01 18:30:18
tags:
  - 技术
  - 一日一练
  - 浏览器
  - Performance
  - 翻译
categories: 技术
---

本文翻译自[Chrome Devtool - Anayze runtime performance](https://developer.chrome.com/docs/devtools/evaluate-performance/)，有部分删减，看原文请到原地址，同时参考了(这篇文章的翻译)[https://zhuanlan.zhihu.com/p/29879682]。

本文的另一个名字是：通过一个[访问在线地址](https://googlechrome.github.io/devtools-samples/jank/)，讲解 Chrome Devtool 的 Performance 的使用。

在线地址`https://googlechrome.github.io/devtools-samples/jank/`

<!--more-->

运行时性能是指页面运行时的性能，而不是加载页面。本教程将介绍如何使用 Chrome DevTools 性能面板来分析运行时性能。就 RAIL 模型而言，这对于分析页面的响应、动画和空闲阶段非常有用。

## 开始
在活动页面上打开 DevTools，使用性能面板查找页面上的性能瓶颈。

1. 以匿名模式打开谷歌浏览器。匿名模式确保 Chrome 在干净的状态下运行。例如，如果安装了很多扩展，那么这些扩展可能会影响的性能表现

2. 在的匿名窗口中加载以下页面。这是要配置的演示。这一页显示了一堆蓝色的小正方形在上下移动。
`https://googlechrome.github.io/devtools-samples/jank/`

3. 按 `Command + Option + I`（Mac）或 `Control + Shift + I`（Windows、Linux）打开DevTools。
{% asset_img 1.png %}

图 1： demo 在左侧，DevTools 在右侧

### 模拟手机 CPU
手机的 CPU 能力比台式机和笔记本电脑小得多。因此在评测页面，都要使用  CPU 节流来模拟页面在手机上性能。

1. 在 DevTools 中，单击 Performancce（性能）选项卡。

2. 确保已启用 Screenshots （屏幕截图）。

3. 单击 Capture Settings （捕获设置）就是一个小齿轮图标。DevTools 揭示了与如何捕获性能指标相关的设置。

4. 对于 CPU，选择 2x slowdown （2x 减速）。DevTools 会对 CPU 进行节流，使其速度比平时慢 2 倍。
{% asset_img 2.svg %}

图 2：CPU 节流，蓝框框出来

### 设置  Demo
为确保创建一致的运行时性能演示。通过自定义功能，确保体验与教程中看到的屏幕截图和描述相对一致。

1. 单击 Add 10，直到蓝色方块的移动速度明显变慢。

2. 单击 Optimize 看到蓝色方块移动得更快更平稳。

注意：如果没有看到优化版本和未优化版本之间的明显差异，请尝试单击 Subtract 10 几次，然后重试。

3. 单击 Un-Optimize 蓝色方块移动得更慢。

### 记录运行时性能
运行页面的优化版本时，蓝色方块移动得更快。为什么呢？两个版本都应该在相同的时间内将每个正方形移动相同的空间量。在性能面板中录制了下来，了解如何在未优化的版本中检测性能瓶颈。

1. 在 DevTools 中，单击 Record 。DevTools 会在页面运行时捕获性能指标。

2. 等待几秒钟。

3. 单击 Stop。DevTools 停止记录，处理数据，然后在性能面板上显示结果。
{% asset_img 4.png %}

## 分析结果
只要记录了页面的性能，就可以测量页面的性能有多差，并找到原因。

### 每秒分析帧数
衡量任何动画性能的主要指标是 frames per second 每秒帧数（FPS）。当动画以每秒 60 帧的速度运行时，用户体验就不错。

1. 看看 FPS 图表。当看到 FPS 上方的红色条时，这意味着帧率下降得太低，可能会损害用户体验。通常，绿色条越高，FPS 越高。
{% asset_img 5.svg %}

图 5：蓝框中的是 FPS 图表

2. 在 FPS 图表下方，可以看到 CPU 图表。CPU 图表中的颜色与 Performance （性能）面板底部的 Summary （摘要）选项卡中的颜色相对应。事实上，CPU 图表充满了色彩，这意味着 CPU 在录制过程中已经耗尽。当看到 CPU 长时间处于最大工作状态时，就应该想办法减少工作量。
{% asset_img 6.svg %}

图 6：蓝框中的是 CPU 图表和 Summary （摘要）选项卡

3. 把鼠标移动到 FPS，CPU 或者 NET 图表之上，DevTools 就会展示这个时间点界面的截图。左右移动鼠标，可以复现当时的屏幕录像。这被称为 scrubbing, 可以用来分析动画的各个细节。
{% asset_img 7.png %}

图 7： 查看记录在 2000ms 标记左右的页面截图

4. 在 Frames 区域中，将鼠标悬停在其中一个绿色长方形上。DevTools 显示该特定帧的 FPS。每帧可能远低于每秒 60 帧的目标。
{% asset_img 8.png %}

图 8： 鼠标移动到帧上

当然这个对于 DEMO，可以相当容易观察到性能的问题。但是在现实使用场景下，就不是那么容易观察到了。所以要把常常使用这些工具来分析页面。

**奖励：打开 FPS 表**

另一个方便的工具是 FPS 表，它在页面运行时提供 FPS 的实时估计。
1. 按 Command + Shift + P（Mac）或 Control + Shift + P（Windows、Linux）打开命令菜单。

2. 开始在命令菜单中键入 `Rendering` （渲染），然后选择 Show Rendering （显示渲染）。
{% asset_img 9.png %}

3. 在 `Rendering` （渲染）选项卡中，启用 FPS Meter。新的遮罩将显示在视口的右上角。
{% asset_img 10.png %}

图 9： FPS Meter

### 查找瓶颈
现在，已经测量并验证了动画的性能不好，接下来要回答的问题是：为什么？

1. 请注意 Summary （摘要）选项卡。未选择任何事件时，此选项卡将显示活动的明细。页面的大部分时间用于 rendering 渲染。提高性能一种做减法的艺术，目标是减少用于 rendering 渲染工作的时间。
{% asset_img 11.png %}

图 10： 蓝框中的是 Summary （摘要）选项卡

2. 展开 Main 部分。DevTools 显示主线程上随时间变化的活动的火焰图。x 轴表示记录随时间的变化。每个 bar 条代表一个事件。更宽的 bar 条意味着这项活动需要更长的时间。y 轴表示调用堆栈（call stack）。当你看到事件堆叠在一起时，这意味着上层事件调用下层事件。
{% asset_img 12.svg %}

图 11： 蓝框中的是 Main 部分

3. 记录中有很多数据。通过单击、按住并将鼠标拖动到 Overview 概述（包括 FPS、CPU 和 NET 图表）上，放大单个 Animation Frame Fired （动画帧触发）事件。Main 部分和 Summary（摘要）选项卡仅显示所选录制部分的信息。
{% asset_img 13.png %}

图 12： 放大单个 Animation Frame Fired （动画帧触发）事件

注意：另一种缩放方式是通过单击 Main 部分的背景或选择事件来聚焦 Main 部分，然后按 W、A、S 和 D 键。

4. 请注意 Animation Frame Fired （动画帧触发）事件右上角的红色三角形。每当你看到一个红色的三角形，这是一个警告，可能有一个与此事件相关的问题。

注意：每当执行 `requestAnimationFrame()` 回调时，就会发生 Animation Frame Fired （动画帧触发）事件。

5. 单击 Animation Frame Fired （动画帧触发）事件。Summary （摘要）选项卡现在显示有关该事件的信息。注意 reveal 链接。单击可使 DevTools 高亮显示启动 Animation Frame Fired （动画帧触发）事件的事件。还要注意 `app.js:94` 链接。单击该按钮将跳转到源代码中的相关行。
{% asset_img 14.png %}

图 13： Animation Frame Fired （动画帧触发）事件的详细信息

注意：选择事件后，使用箭头键选择它旁边的事件。

6. 在 app.update 事件下，有一系列紫色事件。如果它们更宽，看起来好像每个上面都有一个红色三角形。现在单击其中一个紫色 Layout 布局事件。DevTools 在 Summary （摘要）选项卡中提供了有关事件的更多信息。事实上，有一个关于forced reflows （强制回流）的警告（布局的另一个词）。

7. 在 Summary（摘要）选项卡中，单击 Layout Forced 下的 `app.js:70` 链接。DevTools 将跳转到 force the layout 强制布局的代码行。
{% asset_img 15.png %}

图 14：force the layout 强制布局的代码行

注意：此代码的问题在于，在每个动画帧中，它更改每个正方形的样式，然后查询每个正方形在页面上的位置。由于样式发生了变化，浏览器不知道每个正方形的位置是否发生了变化，因此必须重新布局正方形以计算其位置。有关详细信息，请参阅[避免强制同步布局](https://developers.google.com/web/fundamentals/performance/rendering/avoid-large-complex-layouts-and-layout-thrashing#avoid_forced_synchronous_layouts)。

Phew！这是一个很大的需求（Devtools 里面很多指标的探索），但你现在有一个坚实的基础，在基本的工作流程分析运行时性能。

### 奖励：分析优化的版本
使用刚刚学习的工作流和工具，单击演示上的优化以启用优化的代码，进行另一次性能录制，然后分析结果。从改进的帧率到主节火焰图中事件的减少，您可以看到应用程序的优化版本所做的工作要少得多，从而获得更好的性能。




