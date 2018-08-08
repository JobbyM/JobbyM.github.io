---
title: '[React 系列] What Is React'
date: 2018-08-07 16:42:27
comments: true
tags:
  - 技术
  - React 系列
  - 翻译
categories: 技术
---

> 子曰：[React 系列] What Is React

关于本文：
原文：https://learnreact.design/2017/06/08/what-is-react
作者：@[Linton Ye](https://twitter.com/lintonye)

博客系列：用简单的英语和涂鸦解释的React 术语
* [什么是React（本文）](https://learnreact.design/2017/06/08/what-is-react/)
* [什么是React Native](https://learnreact.design/2017/06/20/what-is-react-native)
* [Components，Props and State](https://learnreact.design/2017/08/16/components-props-and-state)
* [重新阐释Props 和State](https://learnreact.design/2018/01/15/props-and-state-re-explained)
* [React Native 同Cordova、PhoneGap、Ionic 等对比](https://learnreact.design/2018/02/14/react-native-vs-cordova-phone-gap-ionic-etc)

React，ReactJS，React.js，React Native ……你最近有多少次听说过这些事情？你对它们的真正含义感到困惑吗？

如果您是团队中使用（或正在考虑使用）React的设计师，或者你只是对这个“React”事物感到好奇，那么这些文章都适合你。

我将使用通俗移动的余元以及涂鸦来解释React 系列中的术语，并深入了解什么使React 如此特别。我希望你先熟悉一些概念，如果你将来需要查看任何内容，可以随时回到这里。

让我们开始吧！

## 学习目标（Learning Goals）
当你看完这篇文章后，希望你能够轻松回答这些问题：

* 什么是DOM？
* 什么是React？它哪里适合开发应用程序？
* React 与jQuery 有何不同？
* React的核心思想是什么？
* 为什么我们需要Virtual DOM？
* 什么是响应式用户界面？
* 组件有哪些好处？

<!--more-->

## 关于Web的几件事（A Couple Of Things About Web）
让我们从你可能已经听过多年的几个术语开始。第一个，DOM。

### DOM
DOM就是文档对象模型。它是文档的对象模型。

让我们看看著名的艺术工作室“Web Browser”！你能在下面的涂鸦中找到DOM吗？
{% asset_img 02-domo-browser.png %}

DOM是是一棵树！计算机中的很多东西看起来都像一棵树。

给DOM 一个昵称Domo。Domo 是“Web Browser”工作室的模特。他的工作是在画一幅肖像画（或者数百万幅肖像画）的艺术家面前摆姿势。

这些肖像是我们在访问网站时在网络浏览器中看到的。开发人员的工作就像是导演告诉Domo 要穿什么以及要做什么姿势。这决定了这些肖像最终的样子。jQuery 或React 被称为库，它们是开发人员用来与Domo 通信的工具。

### jQuery
jQuery 是一个JavaScript 库，使开发人员可以更轻松地操作DOM。它在Domo 的故事中扮演什么角色？

这是一个让开发人员更容易与Domo 交谈的工具，就像手机一样。无论何时何地，你都可以随时给他打电话。它比以前更方便（原生JavaScript）--还记得人们在电话发明之前必须在物理上足够近才能进行对话的时间吗？
{% asset_img 04-jquery-phone.png %}

多年来我们一直使用jQuery 直接与Domo 通信。它很方便但不是没有问题。

### React
向你介绍新的超级英雄，React：
{% asset_img 05-model-agent.png %}

使用React，开发人员不再直接与Domo 交谈。React 充当开发人员和Domo 之间的中间人。他解决了沟通并简化了肖像创作的过程。
{% asset_img 06-middleman.png %}

React 有一些解决jQuery 和其他工具的问题的技巧。以下是三个主要技巧：

* 响应式 UI
* 虚拟 DOM
* 组件

### 响应式 UI（Reactive UI）
使用jQuery 更新DOM，你必须在按时间顺序指定要更改的元素。这相当于向Domo 描述如何为每一幅肖像逐步定位他的头部，手臂和腿部。
{% asset_img 07-step-by-step.png %}

这听起来很乏味且容易出错！为什么你不能告诉Domo 你想要 **什么** 而不是 **如何** 摆pose？
{% asset_img 08-thinker.png %}

更酷的是，想象一下，如果你可以在请求中留下占位符来表示同一姿势的不同变化。React 就是是这样做的！

这样，当有人要求戴着不同帽子的Domo 肖像时，你不必再与Domo 交谈。你可以坐下来让他自己改变它。
{% asset_img 09-thinker-with-hat.png %}

这个技巧使得React 得名。使用React 构建的UI是 **响应的**。作为开发人员，你只需写下你想要的 **内容**，React 就会知道 **如何** 操作。当数据更改时，你的UI 会相应更改。你无需操心更新DOM，React 会自动为你执行此操作。响应式UI 的想法极大地简化了UI 开发。

我说的不需要任何编码知识，只是为了帮助我把问题放在一边，我已经把它放进去了。看看这个例子（尝试改变Domo 的帽子）：

位于CodePen 上的[例子](https://codepen.io/focuser/pen/gROrXx)

我将在以后的文章中解释它的完整代码，此时你可以看看这个核心部分：
{% asset_img code_0.jpg %}

注意，你只需要定义你想要的内容（带帽子的思考者），然后“连接”数据（“`type = {hat}`”）。 当数据更改（用户选择帽子）时，UI 会自动更新。

### 虚拟 DOM（Virtual DOM）
jQuery 的另一个问题是速度。

作为一个要求很高的导演，你讨厌等待。你想让肖像尽快出现。然而，Domo 和艺术家都很慢。Domo 需要时间来换衣服和摆姿势，艺术家需要时间来画画。

更糟糕的是，在再次与Domo 交谈之前，你必须等待艺术家完成一幅肖像。事实上，除了等待，你什么都做不了。
{% asset_img 10-slow-dom.png %}

React提出另一个技巧来解决这个问题。他快速绘制快速草图。几乎在你告诉他你的指示之后，他已经完成了草图并准备接受下一个要求。现在不用再等了！你可以不停地告诉React你想要的肖像，不间断。React 用草图记录所有内容，并在适当的时间将其显示给Domo。
{% asset_img 11-sketches.png %}

更重要的是，React 非常聪明。他可以对草图进行排序，删除任何重复内容，并确保Domo 和艺术家尽可能少地工作。
{% asset_img 12-optimize-sketches.png %}

这些草图称为“虚拟DOM”。虚拟DOM 比DOM 操作快得多。开发人员大多数时间使用虚拟DOM 而不是直接管理DOM。React处理管理慢速DOM的肮脏工作。

### 组件（Components）
React 的第三个技巧是组件的概念。

组件应该易于理解，因为它是我们现实世界的工作方式。我们的汽车，房屋甚至我们的s身体都由可标志的组件组成，这些组件是单独的功能单元。这些组件由较小的组件组成，这些组件由更小的组件组成，一直到原子。

如果您熟悉Sketch，则组件很像[symbole](https://www.sketchapp.com/learn/documentation/symbols/)。在React 中构建应用程序几乎都是关于组件的工作：为作业找到最佳组件，使用另一个组件，从现有组件创建新组件等。

回到我们的工作室“Web Browser”，你描述了对肖像作为组件的要求，React 将其转换为Domo 理解的内容。这样可以节省大量时间，因为你无需在说明中重复常用部件。
{% asset_img 13-components.png %}

组件的另一个很酷的事情是，如果更改一个组件，包含此组件的所有内容都将自动更新。
{% asset_img 14-hair-style.png %}

## 总结（Conclusion）
希望你对React有所了解。它本质上是一个工具，帮助开发人员操纵DOM 来构建用户界面。响应式UI，虚拟DOM 和组件是React 的三个核心思想。当然还有其他有趣的想法，例如单向数据流，我将在以后的文章中展开。

在[下一篇文章](https://learnreact.design/2017/06/20/what-is-react-native/)中，我们将看看ReactJS，React Native和React Sketch.app之类的内容是如何相互关联的。

回到[学习目标](https://learnreact.design/2017/06/08/what-is-react/#learning-goals)，看看你是否能回答所有问题。如果你有任何问题或意见，请与我联系！
---
**想进一步了解React？立即[注册](http://learnreact.design/)并获得独家更新！**
---
## 后台（Backstage）
### 谁画了插图？（Who drew the illustrations?）
很多人问我是不是我画的。好吧，我希望我能。我的涂鸦就像[这样](https://learnreact.design/linton-doodles-2017)。

这些插图由[Beebee Ye](https://beebeeye.github.io/)慷慨制作，他也帮助确保文章真正可以理解。

BTW：学会画画是我的人生目标之一--希望我能在几年内向你展示一些更好的涂鸦。我们会看到......

### 我8 岁的女儿（My 8 yo）
开始我设定了一个目标，这个文章必须足够有趣，能让我8 岁的女儿阅读，并且很容易让她理解一些事情。

至少她可以记住DOM 是一棵树，React与DOM对话。

她还在上面的交互式示例中贡献了一些帽子，并观察我将它们放入代码中。那是黄金时间！猜猜哪些是她画的？我的女儿希望你在[评论](https://learnreact.design/2017/06/08/what-is-react/#endofpost)中回答。

### 特别感谢（Special thanks）
很幸运，这么多人对这篇文章很感兴趣。我要感谢所有花时间阅读早期草稿的人，并给我发了鼓舞人心的话和富有洞察力的建议。

我想对自愿成为这篇文章编辑的[DemingFactor](http://demingfactor.com/)的[Phil Khoo](http://philkhoo.com/)表示深深的谢意。他的建议真的是让这篇文章大放异彩！再次感谢Phil！
---
## 脚注（Footnotes）
---
1. 实际上，计算机不是装满树枝的堆肥容器。树的概念来自于人们如何以一种易于计算机处理的方式组织信息。人们通常会绘制图表，这些图表类似于颠倒的树木，就像这样或那样。
{% asset_img pic_htmltree.gif %}
{% asset_img table.gif %}

2. 在花哨的技术俚语中，如果代码定义了你想要的 **东西**，它就叫做`声明式`;如果它定义了你希望 **如何** 完成它，那就称为`命令式`。`命令式`的编程从计算机最初发明之日起就出现了。当计算机是原始的时，人们必须详细地指导它们：在哪里存储数字，如何相乘等等。一开始很好，但是随着给计算机的任务变得越来越复杂，这种编码变得越来越难以管理。然后人们编写了智能软件，将问题的定义转换为详细的说明。`声明式`程诞生了。今天，越来越多的编程语言和框架（如React）允许我们以声明方式而不是命令式进行编码。
