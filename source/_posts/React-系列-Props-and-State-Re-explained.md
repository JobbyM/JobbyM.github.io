---
title: '[React 系列] Props and State Re-explained'
date: 2018-08-06 16:23:13
comments: true
tags:
  - 技术
  - React 系列
  - 翻译
categories: 技术
---

> 子曰：[React 系列] Props and State Re-explained

关于本文：
原文：https://learnreact.design/2018/01/15/props-and-state-re-explained
作者：@[Linton Ye](https://twitter.com/lintonye)

博客系列：用简单的英语和涂鸦解释的React 术语
* [什么是React ](https://learnreact.design/2017/06/08/what-is-react/)
* [什么是React Native](https://learnreact.design/2017/06/20/what-is-react-native)
* [Components，Props and State](https://learnreact.design/2017/08/16/components-props-and-state)
* [重新阐释Props 和State（本文）](https://learnreact.design/2018/01/15/props-and-state-re-explained)
* [React Native 同Cordova、PhoneGap、Ionic 等对比](https://learnreact.design/2018/02/14/react-native-vs-cordova-phone-gap-ionic-etc)

在[上一篇文章](https://learnreact.design/2017/08/16/components-props-state/)中，我们介绍了组件，属性和状态。

属性和状态之间的差异非常明显，直接确定何时使用属性或状态。例如，屋顶的颜色是属性，因为它是屋顶的内在配置。另一方面，门的状态显然是状态的候选人，因为在建造门之后很容易看到门被打开或关闭。但是，在这篇文章中，让我们挑战这一思路！

你会发现事情可以是属性或状态。没有固定的规则。我将向你展示一种更有用，更实用的思考属性和状态的方法。

<!--more-->

## 学习目标（Learning Goals）
看完这篇文章后，希望你能够轻松回答这些问题：

* 属性、状态各自的主要用例是什么？
* “状态提升（lifting up the state）”是什么意思？ 在什么情况下我们需要状态提升？

## 新客人（New Guest）
注意到我们家附近的新客人了吗？ 点击下面的门！

focuser（[@focuser](https://codepen.io/focuser)）在[CodePen](https://codepen.io/)上实现的[带有猫的React 房子](https://codepen.io/focuser/pen/VydPEJ/)。

这是一只睡着的猫--只要门关闭，她就会睡觉，只有在门打开时才能站起来。如果你关上门，她就会马上睡觉。

## 实现这个猫（Implementing the Cat）
让我们试试如何实现这种行为。

花点时间阅读下面的“代码”。（同样，这不是真正的JavaScript 代码，而是一种简化的符号，可帮助你理解概念）
{% asset_img code_0.jpg %}

House 组件中多了一个Cat 标签。我们来定义Cat 组件的外观。

猫可以睡觉或醒着。这似乎与门的状态非常相似。也许我们也可以用状态来表示猫的状态：
{% asset_img code_1.jpg %}

根据Cat 的定义，上述行为的实现完全是为了同步门的状态和猫的状态。你知道，当门的状态是“打开”时，我们希望猫的状态是“清醒”，否则，猫的状态应该是“睡觉”。

十分简单？ 让我们来看看…

### 尝试一（Attempt 1）
由于我们已经有根据当前状态切换门状态的代码，我们也可以在那里改变的猫状态。
{% asset_img code_2.jpg %}

这不起作用！请记住，状态是组件的私有数据，只能从组件自身访问，无论是父组件还是兄弟组件，都不可以访问组件的状态。

这里我们试图在Door 组件中改变Cat 的状态。它将失败（当然转换为真正的JavaScript代码后）。

### 尝试二（Attempt 2）
在Cat 组件中改变Cat 状态。
{% asset_img code_3.jpg %}

从Cat 组件中更改Cat 状态绝对没问题。但我们需要获取门的状态，来确定猫的状态应该是什么。门的状态是门组件的状态，因此无法从Cat 组件中访问！

### 解决（The Solution）
为了保持门状态和猫状态同步，我们肯定需要在某处访问它们。

解决方案需要我们灵活地理解状态和属性的用例。

### 提升门状态（Elevating Door State）
看看House 组件：
{% asset_img code_4.jpg %}

在这里，门和猫是并排的。也许这就是我们可以轻松同步它们的地方？

但是，我们现在House 组件内部。 同我们上次尝试原因一样，无法读取门的状态或在此处更改Cat 的状态。

但是如果我们使用属性而不是状态呢？
{% asset_img code_5.jpg %}

当门是关闭时，它会这样：
{% asset_img code_6.jpg %}

当然门的状态不能是固定值。它会不时变化。我们用“doorStatus”来表示它。
```
House:
  <div>
    ...
    <Door status={doorStatus} />
    <Cat status={if doorStatus is 'open' then 'awake' otherwise 'sleeping'} />
  </div>
```
这解决同步问题吗？什么改变了“doorStatus” 的值？组件的什么是可以改变的？是状态，对吗？
```
House:
  State: doorStatus <!-- 'open' or 'closed' -->
  <div>
    ...
    <Door status={state.doorStatus} />
    <Cat status={if state.doorStatus is 'open' then 'awake' otherwise 'sleeping'} />
  </div>
```
OK！这个House 组件看起来相当明确，门和猫的状态现在完全同步。

我们还需要使用props 而不是state 更改Door 和Cat 的定义：
{% asset_img code_7.jpg %}

如你所见，因为我们想要使用其父组件的状态，在这种情况下，使用House 内的Door 状态来设置Cat的状态，我们可以表示与父组件状态相同的数据，并将数据作为属性传递给子级。这常被称为 **提升状态（lifting up the state）**。 我们在层次结构中向上移动状态。
{% asset_img elevate-state-24fecc1c464202b8db2d448ceebcd09a.gif %}

### 改变房子状态（Changing House State）
现在，门和猫的状态都与房子的状态有关。如果我们想打开门或唤醒猫，我们需要改变House的状态。

问题，我们可以更新房子状态的唯一地方是什么？在House内，对吗？

但是，我们仍然希望在Door 你触发此更改。也就是说，我们希望门只在门被点击时打开，而不是整个房子或窗户等。

所以Door 组件需要是这样的：
{% asset_img code_8.jpg %}

但等一下，我们不是说不可能从Door 组件内改变House 的状态吗？

那是对的。我们不能在这里直接改变House 的状态。然而，没有什么能阻止我们间接地这样做。看到这个......

在House 组件中，让我们编写一些代码来实际更改自己的状态：
{% asset_img code_9.jpg %}

此时，我们还没有指定何时运行这段代码。我们只为它指定一个名称（“toggleDoorStatus”），以便我们可以按名称选择并在稍后运行。

然后我们把这个名字作为属性传给Door
{% asset_img code_10.jpg %}

在Door 组件中，我们只运行此操作：
```
Door:
  <div>{props.status} door</div>
  when door is clicked
    run props.onClickAction <!-- this runs the code named "toggleDoorStatus" -->
```

这就像将电视遥控器传递给其他人一样。那个人按下Door 组件中的按钮。House 组件中的电视改变频道或增加音量。

将要执行的操作取决于将什么类型的遥控器传递到Door。它可以控制House 中的电视，空调或HiFi 音频系统。在Door 组件中，该人所做的就是按下按钮。
{% asset_img remote-control-e5b10f3429b5cb896cfef52579c2c46a.gif %}

这就是我们所需要的！这是完整的“代码”：
```
House:
  State: doorStatus <!-- 'open' or 'closed' -->
  toggleDoorStatus:
    if state.doorStatus is 'open'
      change state.doorStatus to 'closed'
    otherwise
      change state.doorStatus to 'open'
  <div>
    ...
    <Door status={state.doorStatus} onClickAction={toggleDoorStatus} />
    <Cat status={if state.doorStatus is 'open' then 'awake' otherwise 'sleeping'} />
  </div>
Door:
  <div>{props.status} door</div>
  when door is clicked
    run props.onClickAction
Cat:
  <div>{props.status} cat</div>  
```

## 再次对比Props 和State
现在让我们重新回顾几个问题，props 和state 之间的区别是什么？什么时候应该使用state ，什么时候应该使用props？

### 什么时候使用state ，什么时候使用props？（When to use state, when to use props?）
如果你还记得，我说属性是一个不会改变的组件的内在属性，而状态是在构建组件后可以改变的。在你第一次了解这些概念时会有所帮助。

但是，我们刚刚构建的示例让人感到困惑。门是否打开或猫是否正在睡觉似乎肯定是状态的候选项，但我们用属性表示他们。怎么会这样？

事实证明，在状态和尚属性之间进行选择有很大的灵活性。根据你的看法，你可以找到不同的组件建模方法。例如，你可以说门的状态是打开的，或者房子的状态是门是打开的。

### 一种更有用的理解方式（A more useful way to understand）
困惑？ 这是一个更有用的思考问题的方法：

* 状态：UI 需要改变=>某处应该有一个状态。
* 属性：使用它们传递数据和传递控制

如果在应用程序运行时UI 发生了某些变化，则必须是状态。如果你在单击时看到门打开和关闭。某个地方必须有一个状态。

但是，状态不一定需要位于在更新的组件上。它可能在某个上游。 这一切都取决于我们需要在何处以及如何使用这些信息。例如，我们决定将门的状态从Door 组件提升到House 组件，因为我们需要在House 组件中使用它。

另一方面，属性只是用于在层次结构中向下传递数据的东西。就像我们将状态isDoorOpen 的值传递给Door 组件所做的那样。

属性也可用于传递控制权。例如，我们从House 到Door 传递了事件处理程序。

### 但在这种情况下，属性会改变值吗？（But do the props change value in this case?）
不，他们现在不会，将来也不会。我知道，门打开和关闭，猫睡觉醒来。因为我们现在使用属性来表示这些，所以很容易认为属性的行为就像状态一样--它们的值会发生变化，对吗？

这只是一种幻觉，我发现它与flipbook 动画相似。
{% asset_img flipbook-0d8eaaae5b49719d95feb33b3a586da5-f8fb9.jpg %}

每当House 的状态发生变化时，旧猫就会被移除，并且从头开始创建一个具有新状态的全新猫。但是这个过程发生得非常快，这让我们产生了一种错觉，即只有一只猫睡着了醒来。

翻书中任何页面上的草图都不会移动。同样，猫在其短暂的生命周期内保持清醒/睡眠状态。

## 总结（Conclusion）
好吧，我们用一个更复杂的例子研究了属性和状态。在这个例子中，我们必须保持门和猫的状态同步，而且门在点击时仍然应该打开或关闭。

因为状态是私有的，所以我们决定将门的状态从门组件的状态提升到House 组件的状态。这样，我们就可以在House 组件中使用这些数据来设置门和猫的状态。我们将此数据作为属性传递给Door 和Cat，所以他们可以根据门状态显示正确的图像。

另一个要求是在单击门时触发状态更改。因为门的状态现在是House 私有的状态，所以从Door 内部我们只能间接地进行更改。在House 中，我们编写实际更改状态的代码，并将其名称传递给Door。

这篇文章中的例子可能会让事情有点混乱。 这是一种思考状态和属性的实用方法：

* 状态：UI 需要改变=>某处应该有一个状态。
* 属性：使用它们传递数据和传递控制

目前感觉如何？请随时留下你的意见和问题！
