---
title: '[React 系列] components props and state'
date: 2018-08-03 12:53:39
comments: true
tags:
  - 技术
  - React 系列
  - 翻译
categories: 技术
---

> 子曰：[React 系列] components props and state

关于本文：
原文：https://learnreact.design/2017/08/16/components-props-and-state
作者：@[Linton Ye](https://twitter.com/lintonye)

博客系列：用简单的英语和涂鸦解释的React 术语
* [什么是React ](https://learnreact.design/2017/06/08/what-is-react/)
* [什么是React Native](https://learnreact.design/2017/06/20/what-is-react-native)
* [Components，Props and State（本文）](https://learnreact.design/2017/08/16/components-props-and-state)
* [重新阐释Props 和State](https://learnreact.design/2018/01/15/props-and-state-re-explained)
* [React Native 同Cordova、PhoneGap、Ionic 等对比](https://learnreact.design/2018/02/14/react-native-vs-cordova-phone-gap-ionic-etc)

今天我们研究React 中三个最重要的概念：组件（component），属性（props）和状态（state），以及属性和状态之间的差异。

同前文一样，我将使用通俗的语言解释这些概念。因此，阅读这篇文章不需要JavaScript 知识。

<!--more-->

事实上，这里没有任何JavaScript 代码。在深入了解JavaScript 之前（在之后的文章中）我将使用简单的符号来帮助你了解主要思想。当你没有太多的编程经验--容易弄混React 概念和JavaScript的微妙之处--这种填鸭式教育方法是学习React（或任何技术）的有效方法。

## 学习目标（Learning Goals）
当你看完这篇文章后，希望你能够轻松回答这些问题：

* 什么是属性？
* 什么是状态？
* 什么时候使用属性和状态？
* 如何使窗户打开？
* 使用伪代码描述[Domo的帽子](https://learnreact.design/2017/06/08/what-is-react/#domo-hat)？

## 搭建房子（Let's build a house）
通过一个简单的例子，来了解这些概念的含义以及如何使用它们。[位于 CodePen 中的代码](https://codepen.io/focuser/pen/EXBOpx)是一个房子（试着点击门）。

## 组件（Components）
在[之前的文章中](https://learnreact.design/2017/06/08/what-is-react/)讨论过组件是React 的三大支柱之一。使用React 构建应用程序几乎都是关于使用组件。

第一步是将UI 分解为各种组件，我们可以如下分解我们的房子：
{% asset_img 1-house-components-fb412ee12e451ff23d407372bfb6d2b7-dc9cd.png %}

现在开始编码！
{% asset_img code_0.jpg %}

React 代码的某些部分看起来与HTML 非常相似，并且在设计上，它是为了让Web 设计人员能够轻松理解和编写代码。

在上面的代码中，我们有`<div>` 容器，它与HTML中的几乎相同。像`Roof` 和`Wall` 是我们即将定义的自定义标签/组件。

需要注意，上面的内容并不是真正的React 代码--根本就没有JavaScript。现在，简单地解释这些内容。首先理解场景背后的想法，下一步就是学习JavaScript 的细节，并将想法转化为真实的代码。

为了更容易理解，我将使用另一种简化方式：而不是在本文开头那样使用图像，现在让我们创建一个超级简单的Web 应用程序，显示所有内容是纯文本。

下面表示屋顶：
{% asset_img code_1.jpg %}

其他组件是相同的--它们只是HTML div中的纯文本。下面是完整的React-ish 代码：
{% asset_img code_2.jpg %}
{% asset_img code_3.jpg %}

房子由屋顶，墙壁，窗户和门组成，而它们由纯文本组成。

最后，React 将生成如下HTML：
{% asset_img code_4.jpg %}

## 使用属性配置屋顶的颜色（Configure roof color with Props）
想想一下，我们将一张规格表单发送到建造零件的工厂。在规格表单中，我们告诉工厂有关零件的内在属性：屋顶的颜色，门的形状等。根据我们的要求建造屋顶和门后，这些属性保持不变。屋顶是蓝色的，门是一个矩形。

在React 中，我们将这些称为属性（Props）。关于Props 的两件事：首先，我们确定属性的值，并在构建组件之前将其用作蓝图的一部分。其次，属性的值永远不会改变。

属性在代码中是怎么样的呢？在`House` 组件中，如果我们希望屋顶是蓝色的，我们可以简单地在屋顶标签上添加属性“颜色（color）”。 就像指定要发送到工厂的规范中的颜色一样。

它看起来非常类似于可以添加属性的HTML 标签：
{% asset_img code_5.jpg %}

我们为屋顶编写代码。
{% asset_img code_6.jpg %}

在这里有一些值得一提的事情：

* 定义组件的HTML-ish 代码是 **模板（template）**，而不仅仅是单个HTML 标记。 这意味着我们可以在其中放置 **占位符（placeholder）** 以输出HTML内容的变量而无需重复。（[这张Domo的帽子图片](https://learnreact.design/images/what-is-react/09-thinker-with-hat.png)就是占位符的思想！）在这个例子中，对于`<Roof color="blud" />`，我们将得到`<div> blue roof <div>`; 对于`<Roof color="red"/>`，我们将得到`<div> red roof </div>`，依此类推。
* 模板中使用的花括号告诉React 我们将在这里使用占位符，而不是普通内容。
* `props` 可以被认为是`Roof` 标签中设置的所有prop 值的容器。假设我们有`<Roof color="blue" material="wood" />`，我们可以在`Roof` 组件的定义中使用`props.color` 和`props.material`。

## 使用状态开门（Open the door with State）
### 在组件中添加状态（Add state to a component）
组件可以拥有状态（State）。什么是状态？它可以在构建组件后进行更改。

例如，门可以是打开的或关闭的。 我们说门的状态（Status）是一个状态（State），因为即使在门建成后它的值也会改变。这与门的形状不同，门的形状一旦创建就不会改变。

状态值的改变通常是由一些外部事件引起的。对于Web 应用程序，通常是用户输入，或来自服务器的数据或时间流逝来改变组件状态的。

让我们把状态添加到门：
{% asset_img code_7.jpg %}

与`props` 类似，`state` 是组件中所有状态值的容器。因此，我们可以在组件定义中的模板内使用`state.[something]`。

### 根据用户输入改变状态（Change the state on user input）
接下来，让我们通过添加一些处理用户输入的“代码” 来实现门的交互。
{% asset_img code_8.jpg %}

这里的关键是组件的状态不时变化。模板的输出，即生成的HTML，相应地自动改变。

BTW：别忘了以上不是真正的React 代码。不要将其复制粘贴到你的项目中！

### 状态是私有的（State is private）
状态是组件私有的。门是打开还是关闭只是门的业务逻辑。它与房屋或其他组件无关。 事实上，我们可以把门从房子里取出来，它仍然可以自己打开或关闭。

因此，门的状态仅在门组件内可见。在`Door` 组件中，我们只能读取或更改自己的状态。
{% asset_img code_9.jpg %}

## 总结（Conclusion）
属性是组件的配置，其值在创建组件之前确定。就像门的形状或屋顶的颜色一样，属性始终保持不变。另一方面，状态是组件的私有数据，只有在创建组件后才可用。就像门是打开还是关闭一样，状态会不时变化。通常它响应用户的输入，或者是服务器上发生的事情，或者是时间的推移。

但是我们还没有建立任何真实的东西。另外，创建一个只显示纯文本的应用程序有多大用处？我知道，至少你想看看如何建造你在文章开头看到的房子--你可以点击，对吧？

这需要JavaScript 编码，我将把它留给以后的文章。如果你不想等待，请注册[我的课程](https://learnreact.design/react-101)视频。

## 脚注（Footnotes）
---
1.在技术方面，不是真实代码的描述主要思想的符号称为伪代码。伪代码不适合计算机执行，是我们人类形象化思想和与他人交流的工具。记住，伪代码不能在计算机上运行，它是假代码。
