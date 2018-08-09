---
title: '[React 系列] What Is React Native'
date: 2018-08-08 08:51:32
comments: true
tags:
  - 技术
  - React 系列
  - 翻译
categories: 技术
---

> 子曰：[React 系列]  What Is React Native

关于本文：
原文：https://learnreact.design/2017/06/20/what-is-react-native
作者：@[Linton Ye](https://twitter.com/lintonye)

博客系列：用简单的英语和涂鸦解释的React 术语
* [什么是React](https://learnreact.design/2017/06/08/what-is-react/)
* [什么是React Native（本文）](https://learnreact.design/2017/06/20/what-is-react-native)
* [Components，Props and State](https://learnreact.design/2017/08/16/components-props-and-state)
* [重新阐释Props 和State](https://learnreact.design/2018/01/15/props-and-state-re-explained)
* [React Native 同Cordova、PhoneGap、Ionic 等对比](https://learnreact.design/2018/02/14/react-native-vs-cordova-phone-gap-ionic-etc)

在[上一篇文章](https://learnreact.design/2017/06/08/what-is-react/)中，我们研究了React 是什么以及它的特殊之处。今天让我们来研究React Native：它的用途是什么，它来自哪里，它与React的不同之处以及它为什么了不起。

## 学习目标（Learning Goals）
当你看完这篇文章后，希望你能够轻松回答这些问题：

* 什么是React Native？ 为什么它的名字包括“Native”？
* 为什么React Native 很酷？
* 我们可以用React Native 构建什么？React 怎么样？
* ReactDOM 来自哪里？它有什么作用？
* React Renderer 有什么作用？
* React Sketch.app 如何工作？
* ReactVR 如何工作？
* 什么是ReactJS？React.js？

<!--more-->

## 超越Web（Beyond The Web）
到现在为止，你可能已经掌握了这张照片：
{% asset_img 1-react-summary.png %}

你知道，React是一个在Web上构建用户界面的工具。使用React，你可以通过描述你想要的内容来构建UI，而不是如何更新UI（响应式UI），在可重用组件中组织代码，以及创建高性能UI而无需担心DOM 慢速（虚拟DOM）。越来越多的Web 开发人员选择React，因为它允许他们专注于更大的方向而不是低级细节。我们称这种构建UI方式为React 范式。范式基本上是你如何思考问题以及如何描述问题及其解决方案。

这对web 应用来说非常棒。那么其他平台呢，比如iOS和Android？如果你可以将相同的React 范例应用于移动应用程序的开发，那不是很好吗？

在某种程度上，移动平台的工作方式与Web 相同。有一个模型（一个树）。有些东西可以根据模型（艺术家）创建视觉元素。毫不奇怪，构建移动UI 的传统方式是直接操作树模型并告诉它如何更新事物（直接与树交谈）。这与在Web浏览器中直接使用DOM具有类似的缺点。React 完全有助于此。

除了相似之外，移动平台与Web 不同，移动平台之间也彼此不同。过去，开发人员必须学习平台的特定语言和工具链才能为其构建应用程序。

这就像在异国情调的工作室工作，工作人员会说各种不同的语言。你必须学习与这些模型进行通信的所有新语言。这听起来不容易，是吗？
{% asset_img 2-ios-android.png %}

因此，如果你想为iOS 和Android 构建本地应用程序，则必须创建两个完全独立的代码库。必须实现两次相同的业务逻辑。创建应用程序既困难又昂贵，从长远来看，维护它更是如此。

这就是React Native 创建的原因。让我们看看它如何让事情变得更容易。

## React Native
### 渲染器和新的React（Renderers And The New React）
对于Web 应用程序，React 负责启用React 范式（管理响应式UI，组件和虚拟DOM），以及在浏览器中实际更新DOM（与Domo 交谈）。当DOM 是唯一需要交互的东西时，React可以轻松处理这两项任务。

但是，对于移动应用程序，当需要在不同平台上管理各种树模型时，事情变得具有挑战性。如果我们继续给React 增加更多的责任，它将开始让我们可怜的超级英雄疯狂。
{% asset_img 2.5-crazy.png %}

为了解决这个问题，React 的创建者将原来的React 拆分为两部分。第一部分是新的React，重点是启用React 范式。第二部分称为ReactDOM，其唯一的工作是在浏览器中与DOM 交互。因为ReactDOM 更新了确定浏览器上呈现内容的DOM，所以我们说ReactDOM是一个渲染器。

回到我们的故事，想象一下，我们的超级英雄脱下他的斗篷，撒上一些魔法尘埃。
{% asset_img 3-cloak-and-dust.png %}

他的斗篷成为他的帮手。摆脱了与Domo 打交道的负担，React现在可以专注于他所做的最好的事情。
{% asset_img 4-reactdom-domo.png %}

这种角色分离是一个高效的想法。它可以轻松编写适应新平台的新渲染器，同时保持共享核心。在iOS和Android渲染器的支持下，你现在可以使用 **单一语言和相同的React 范式** 为两个平台构建应用程序。
{% asset_img 5-renderers.png %}
React 专注于他的事情。渲染器做连接。

### 一个完整的平台（A Complete Platform）
React 的官方定义是：用于构建用户界面的JavaScript 库。它的意义是双重的：首先它是UI 的一个很好的工具，其次它不包括任何其他东西。

实际上，**单独使用React就无法构建完整的应用程序**。例如，你需要用于样式的CSS，用于预处理和打包代码的webpack，用于保存数据的Firebase 以及许多其他内容。
{% asset_img 6-other-folks-at-web.png %}

工作室“Web Browser”实际上比你看到的要繁忙得多。

这在Web 开发环境中很好，因为作为一个JavaScript 库，React 自然适用于其他部分，这些部分既可以是JavaScript 库，也可以与JavaScript库配合使用。毕竟，JavaScript 是Web 语言。

但是，对于使用各种语言和技术的移动平台而言，情况有所不同。在JavaScript 中包含一整套工具变得非常重要，这些工具可以像React 一样使用。因此，React Native 诞生了。

与Web 的React相比，React Native 包含更多内容：
* 新的React 作为它的核心（我们的没有斗篷的超级英雄），
* 适用于iOS 和Android 的渲染器，
* 将代码转换并打包到可安装应用程序中的工具，
* 本地UI 部件（状态栏，ListView等）和动画，
* 用于样式和布局UI（flexbox）的工具包，
* 构成大多数应用程序（如网络）的基本部分，
* 提供原生功能的部件，如剪贴板，加速感应器和存储。
* ...

我们说React Native 本身就是一个完整的平台，因为它包含了构建完整应用程序所需的一切。相比之下，最初的React只负责Web UI，你需要自己包含其他部分来创建Web应用程序。
{% asset_img 7-summon.png %}

React Native 的组成

### 本地UI（Native UI）
你有没有想过为什么React Native 被标记为Native？这实际上是它的品牌特征：使用React Native 构建的UI 包含 **本地UI小部件**，这些小部件表现良好且外观/感觉一致，而不是WebView 中包含的一些模拟实现。使用React Native 构建的应用程序通常与使用Swift和Java等本机语言编写的应用程序无法区分。

滚动加速，动画，键盘行为和阴影这些小东西实际上在你的应用程序的用户体验中起着重要作用。如果这些与手机上的其他应用程序不一致，用户很快就会感到困惑。

我原本打算在这里解释一下“本地”的真正意义以及为什么React Native 表现更好。但经过几次头脑风暴会议之后，我的笔记很快就变成了整页。让我把它作为自己的之后帖子。

现在，我希望你能记住，原生用户界面是让React Native 大放异彩的好处之一。

所以在这里，React Native 是一个完整的平台，允许你在JavaScript 中使用React 范式构建真正的本地应用程序。
{% asset_img 8-react-native-summary.png %}

## React Sketch.app, ReactVR, React XYZ…
最近Airbnb发布了一个名为[React Sketch.app](https://airbnb.design/painting-with-code/)的漂亮工具，它将React 代码转换为Sketch 中的图像层。你能猜出它是如何工作的吗？

它本质上是React Native，在Sketch中,它使用一个渲染器与树模型对话！
{% asset_img 9-sketch.png %}

因为React Sketch.app 是基于React Native，它是一个完整的包，我们可以做一些有趣的事情，比如从真实的API 中获取数据并在Sketch中呈现它。

与此同时，React Native 的许多其他变体被创建用于支持构建[Windows](https://github.com/Microsoft/react-native-windows)，[macOS](https://github.com/ptmt/react-native-macos)，[VR](https://github.com/facebook/react-vr)等应用程序。

这意味着，只要你掌握了React 的精髓，就可以使用相同的思维模式使用JavaScript 为数以千计的平台（还在增长）构建应用程序。正如React Native的创作者所说，**“学习一次，随处写作（learn once, write anywhere）”**。

## 动手时间！（Hands-on Time！）
到目前为止说了这么多！你想在手机上试试吗？

我也很兴奋！拿起住你的手机跟着我吧！

1.在手机上，下载Expo应用程序。你可以从这里下载：[iOS](https://itunes.apple.com/app/apple-store/id982107779?pt=17102800&ct=www&mt=8)，[Android](https://play.google.com/store/apps/details?id=host.exp.exponent)，或在App Store中搜索“Expo”。该应用程序的图标看起来像
{% asset_img expo-ios.jpg %}（或
Android上的{% asset_img expo-android.png %}）。
2.在你的计算机上，打开此页面：https：//snack.expo.io/
3.在手机上，启动Expo应用程序并点按“扫描QR码”
4.扫描计算机上显示的QR码。如果一切正常，你应该看到绿色的“设备已连接”消息。
5.如果QR 码不会自动消失，请单击页面右上角的小X将其关闭。你应该看到一个代码编辑器。
6.删除编辑器中的所有内容，粘贴[此代码](https://gist.githubusercontent.com/lintonye/5cb3c11349591bf475b91573682fe688/raw/4ea0d4562ce45c1ba3867a1759359e1f86bb590a/domohat.jsx)。
7.你在手机上看到了什么？
8.如果需要，你可以在编辑器中更改代码，并立即在手机上查看结果！

我将会详细解释这个环境。现在，请记住它就像React Native 的Codepen（我曾经在上一篇文章中向你展示过Domo的帽子示例的工具）。

如果将[React Native 版本](https://gist.github.com/lintonye/5cb3c11349591bf475b91573682fe688)的代码与[React（Web）版本](https://codepen.io/focuser/pen/gROrXx)进行比较，你会发现它们看起来非常相似。它们都是这样的：
{% asset_img code_0.jpg %}

记住“Learn once, write anywhere”!

## ReactJS和React.js怎么样？（What About ReactJS And React.js?）
你可能已经听过很多ReactJS（或React.js）--我也使用过它。事实上它从来都不是官方名称.从[第一天起](https://web.archive.org/web/20130529213355/https://facebook.github.io/react/)，官方名称一直是“React”。

因为JavaScript 库往往被命名为“XyzJS”或“Xyz.js” 而React 是一个JavaScript 库，所以人们可能会自愿将“JS”或“.js”附加到其名称中。由于React 最初是一个Web 库，很多人使用ReactJS或React.js来引用Web 上的React，包括React 和ReactDOM。

遵循事实上的惯例，当我说ReactJS 时，我的意思也是Web 上的React。

## 结论（Conclusion）
到目前为止，我们已经完成了很多事情。 我们已经了解了React 的一些历史以及React Native 如何组合在一起。作为一个完整的平台，React Native 包含了使用JavaScript 和React 范例中构建本地应用程序所需的一切。React Native 现在支持许多平台，包括iOS，Android，Windows，macOS，Sketch.app甚至VR。 “Learn once, write anywhere”！

在下一篇文章中，我们将了解本地应用程序的真正含义以及为什么React Native是构建本机应用程序的最佳方式之一。

我鼓励你回到[学习目标](https://learnreact.design/2017/06/20/what-is-react-native#learning-goals)，看看你是否能回答所有问题。如果你有任何问题或意见，请与我联系！
---
**想进一步了解React？立即[注册](http://learnreact.design/)并获得独家更新！**
---
## 后台（Backstage）
### 插图（Illustration）
我很幸运能与[Beebee Ye](https://beebeeye.github.io/)合作，他是一位优秀的插画家和讲故事的人。他的插图使这些文章非常独特，阅读有趣。

### 修订（Revisions）
像之前一样，这篇文章经历了很多编辑。这里有什么特别之处，就是我几乎完全改写了听到的所有事情，我把这些比喻都搞得太过分了。

我非常感谢你的建设性建议，这些建议促使我改写它，[Yitong Zhang](https://twitter.com/Yitong_Zhang)，Brittany Smart，[Grant Robinson](https://disqus.com/by/lostpixel/)，Jason Fuller和Victoria Pugh。 万分感谢！

如果你很好奇，请查看之前充满幻想故事的[版本](https://learnreact.design/2017-06-20-what-is-react-native-metaphor/)。

## 脚注（Footnotes）
1.实际上，它会让React的维护者疯狂。
2.好吧，斗篷是我能想到的唯一可以从超级英雄中分离出来而没有任何伤害的东西。忍受我！
3.还记得声明式和命令式编程吗？ 它们都是范式。**React 范式** 是关于如何将UI分解为组件，如何传递数据等等，我们将在后面详细研究。
4.如果你正在寻找React Native和React Web之间的深入技术比较，请查看此[文章](https://medium.com/@alexmngn/from-reactjs-to-react-native-what-are-the-main-differences-between-both-d6e8e88ebf24)。
5.React Renderers 的想法非常强大。这里有一个很酷的渲染器[列表](https://github.com/chentsulin/awesome-react-renderer)，可以使用React 构建许多不同的东西。
6.在NPM（JavaScript 库的中央存储库）中，你可以找到[reactjs](https://www.npmjs.com/package/reactjs) 和[react](https://www.npmjs.com/package/react)。 你能说出哪一个是真正的React吗？
