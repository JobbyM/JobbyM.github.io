---
title: '[React 系列] React Native 同Cordova、PhoneGap、Ionic 等对比'
date: 2018-08-09 09:27:50
comments: true
tags:
  - 技术
  - React 系列
  - 翻译
categories: 技术
---

> 子曰：[React 系列] React Native 同Cordova、PhoneGap、Ionic 等对比

关于本文：
原文：https://learnreact.design/2017/06/20/what-is-react-native
作者：@[Linton Ye](https://twitter.com/lintonye)

博客系列：用简单的英语和涂鸦解释的React 术语
* [什么是React](https://learnreact.design/2017/06/08/what-is-react/)
* [什么是React Native](https://learnreact.design/2017/06/20/what-is-react-native)
* [Components，Props and State](https://learnreact.design/2017/08/16/components-props-and-state)
* [重新阐释Props 和State](https://learnreact.design/2018/01/15/props-and-state-re-explained)
* [React Native 同Cordova、PhoneGap、Ionic 等对比（本文）](https://learnreact.design/2018/02/14/react-native-vs-cordova-phone-gap-ionic-etc)

在[上一篇文章](https://learnreact.design/2017/06/20/what-is-react-native)中，我们知道React Native 很棒，因为它允许我们使用本地UI 构建应用程序。React Native 应用程序的用户体验比使用WebView UI 要好得多。 但“本地（native）”究竟意味着什么？ 什么是WebView UI？ 为什么本地UI 比WebView UI 更好？ React Native与其他移动框架（如PhoneGap，Cordova和Ionic）比较怎样？

让我们现在深入研究这些问题。

## 学习目标（Learning Goals）
当你看完这篇文章后，希望你能够轻松回答这些问题：

* 什么是本地应用？
* 什么是WebView UI？
* 更本地框架有哪些好处和缺点？较少的本地框架怎么样？
* React Native 与Cordova 比较如何？

<!--more-->

## 什么是“本地性”应用程序，反之呢？（What is a "native" app, anyways?）
要了解这种“原生（nativeness）”实际意味着什么，我来告诉你：
{% asset_img matrix.png %}

我是黑客帝国的忠实粉丝。我们所知道的现实实际上是一种模拟。有人提到，即使锡安，人类曾经认为的最后一个城市，也是一个模拟。多层模拟现实的想法一直让我着迷--一个虚拟世界在另一个虚拟世界中作为模拟运行在第三世界里面。

这很像我们的计算机（或电话）中的软件结构。

软件是如何操作大量晶体管和电路的指令的安排，统称为硬件。直接在硬件上运行的原始指令对我们人类来说是难以理解的，特别是考虑到当今计算机的复杂性和规模。

为了使软件易于理解和可操作，计算机科学家将其分为多个层，这些层由运行在另一个框架之上的框架组成。在所有这些框架中，框架越接近硬件，我们就说它更“本地”。
{% asset_img relative-native.png %}

因此，应用程序的“本地性”实际上是一个相对术语。严格地说，我们不能说应用程序本身是否是本地的。我们只能说，与其他应用相比，这款应用更具本地性。例如，你可以使用C++，Java（或Kotlin）或Cordova 构建Android 应用程序。C++ 应用程序是最本地的，Cordova 应用程序是最不本地的。Java/Kotlin应用程序介于两者之间。

## 一个更本地的应用程序有什么好处？（What's good about a more native app?）
好处要么是更多本地的要么是较少本地的。更接近物理层意味着更多的自由，而更多的模拟让你更舒适。
{% asset_img steak.png %}

更本地的框架中的程序通常可以更多地访问硬件功能，并且可以更自由地使用它们。它通常运行效率更高，因为不同语言之间的模拟和转换开销较低。但是基础现实中的生活可能很苛刻--代码通常更难以编写和理解。

另一方面，我们人类通常更容易在较少的本地框架中编写代码。编码语言更容易理解，更简洁（需要更少的输入）。它的词汇量更接近我们的自然语言。它需要较少了解硬件的构成以及它在引擎下的工作方式。作为一个额外的好处，较少本地框架中的程序通常更具可移植性--程序可以在完全不同的硬件平台上运行而无需修改，因为它的词汇和底层概念不包含任何特定于原始硬件的内容。然而，所有这些便利的代价是你通常会牺牲一些效率和自由。

## 移动框架阵营（Mobile framework camps）
在React Native 之前，移动框架通常属于两个阵营中的一个。

第一个是本地阵营，例如Android 的Java/Kotlin 和iOS 的Objective-C/Swift。在本地阵营中，应用程序速度快，可以访问丰富的硬件功能。用户界面是针对目标平台（Android 或iOS）的定制，因此非常流畅且使用愉快。然而，所有这些优点仅限于一个平台。要构建应用程序，必须学习两种不同的框架，这使得学习曲线陡峭两倍。这尤其会让数百万网络开发人员落伍。

然后是另一个移动框架阵营，例如Cordova/PhoneGap 和Ionic。这些框架允许Web 开发人员使用其现有的HTML，CSS 和JavaScript 技能构建移动应用程序。这些应用可以在Android 和iOS（以及更多）上运行。但是，它们更加缓慢，并且对硬件功能的访问受限。最重要的是，这些应用程序的用户界面很糟糕！由于这些框架使用一个名为WebView 的关键组件来呈现UI，因此我们将它们称为WebView 框架。

WebView 框架构建在本地框架之上。我们可以看到前者是在后者内部运行的模拟世界。这就是为什么他们有上述的好处和限制。
{% asset_img webview.png %}

为什么我们不能从中获益并避免两者的陷阱？这就是React Native 要做的事情。

React Native 代表了移动框架的第三阵营。它的UI 层比WebView 框架更本地，而其余部分则处于相同的模拟级别，以实现其易用性。

### React Native UI 比WebView UI 更本地（React Native UI is more native than WebView UI）
Cordova 这样的框架可以使用Web 技术构建移动UI。他们是如何做到的呢？他们在每个应用程序中嵌入了一个名为WebView 的Web 浏览器！你在UI 中看到的所有内容，按钮，菜单和动画都在浏览器的网页中运行。在模拟方面，Cordova 应用程序的UI 是在Web 浏览器中运行的模拟世界，Web 浏览器是在本地框架内运行的模拟世界。

相比之下，React Native 的UI 是比WebView 框架更本地的一层--它直接在本地框架内运行。
{% asset_img rn.png %}

此结构决定了React Native UI 的优点。 React Native 直接使用本地UI 小部件，而WebView 框架尝试使用HTML/CSS 中的Web UI 模仿本机UI 的外观。真实的东西或假的东西，你更喜欢哪一个？

根据经验，很容易识别使用WebView 框架构建的应用程序。那些小东西，比如滚动加速，键盘行为，导航和UI 的平滑度。如果他们没有完全本地化的行为，事情就会增加并使用户沮丧。

### React Native 使用JavaScript 来简化操作（React Native uses JavaScript to make things easier）
另一方面，React Native 仍然允许我们用JavaScript 编写应用程序，并使用类似于HTML 和CSS 的语法来构建UI。这无疑降低了网页设计师和开发人员的入门门槛。

必要时，React Native 提供了一种渗透到本地框架的方法，以实现我们希望在应用程序中实现的任何本地功能。这有点像在黑客帝国中拨打电话。
{% asset_img phonecall.png %}

## 那么WebView 框架一文不值吗？（So are WebView frameworks worthless?）
不，绝对不是。例如，如果你已经拥有一个Web 应用程序，并希望尽快将其发布到应用程序商店。你愿意牺牲用户体验来尽快发布。

另一种情况是，如果应用程序中显示的内容主要是内容没有太多的交互性，那么将它放在WebView 中并不是太糟糕。

但是，如果你的应用程序的用户体验很重要，如果应用程序中存在某些交互性，例如接受用户输入，拖放，滑动屏幕等，请务必考虑React Native。因为最重要是使用WebView UI 模仿本地UI。

## 总结（Conclusion）
我们已经看过“本地（native）”的真正含义，WebView UI 是什么以及为何React Native UI 更好，React Native 与其他WebView框架（如Cordova / PhoneGap和Ionic）的比较。

你将使用哪个框架来构建你的下一个应用程序？
{% asset_img redpill-bluepill.png %}

---
## 脚注（Footnotes）
---
1.在黑客帝国2：重装上阵（Matrix Reloaded）结束时，即使在锡安，Neo也显示出控制和击落哨兵的能力。虽然在黑客帝国3：矩阵革命（The Matrix Revolution）没有明确解释这一点，但当时很多粉丝都认为即使是锡安也是模拟的。
2.毕竟，正如伊隆马斯克所说，我们在基础现实中有数十亿的机会。
3.让我们在这篇文章中松散地使用术语“框架（framework）”来指代具有某种结构的软件。
