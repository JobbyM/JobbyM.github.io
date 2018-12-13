---
title: 一日一练-JS AST抽象语法树
date: 2018-12-12 11:13:32
comments: true
tags:
  - 技术
  - 一日一练
  - JS
  - AST
  - 翻译
categories: 技术
---

{% asset_img hero.png %}

关于本文：
原文：https://itnext.io/ast-for-javascript-developers-3e79aeb08343
作者：@[Bohdan Liashenko](https://itnext.io/@bohdanliashenko?source=post_header_lockup)
译者：@[JobbyM](https://github.com/jobbym)
文章带有译者的理解，更多内容请查看[原文](https://itnext.io/ast-for-javascript-developers-3e79aeb08343)

>TL;DR 这篇文章是在Stockholm ReactJS Meetup 上的演讲，可以在[https://www.slideshare.net/BohdanLiashenko/ast-for-javascript-developers](https://www.slideshare.net/BohdanLiashenko/ast-for-javascript-developers) 获取slides

<!--more-->

## 为什么讲抽象语法树（Abstract Syntax Tree，AST）
查看现代项目的devDependencies，将看到它在过去几年中飞速增长。 我们在那里看到了一系列工具：JavaScript转换，代码压缩，CSS预处理器，eslint，prettier 等等。这些是不需要部署到生产环境的JavaScript 模块，但它们在我们的开发过程中起着非常重要的作用。所有这些工具都以各种方式构建在AST 处理之上。
{% asset_img ast_0.png %}

介绍一下要讨论的内容。首先从什么是AST 开始，以及如何从普通代码构建它。然后，我们将略微触及一些在AST 处理之上构建的最流行的用例和工具。最后是关于我的项目js2flowchart 的讨论，这是使用AST 时可以构建的最好的演示。接下来，让我们开始吧。
{% asset_img ast_1.png %}

## AST 是什么？
>It is a hierarchical program representation that presents source code structure according to the grammar of a programming language, each AST node corresponds to an item of a source code.

它是一种分层程序表示，根据编程语言的语法呈现源代码结构，每个AST 节点对应一段源代码。
{% asset_img ast_2.png %}

好了，让我们通过一个例子来了解它。
{% asset_img ast_3.png %}

但这是主要的想法。从纯文本中，我们得到了树状数据结构。一段代码与树中的节点匹配。

如何从普通代码中获取AST？好吧，我们知道编译器已经这样做了。我们来看看普通的编译器吧。
{% asset_img ast_4.png %}

幸好，我们不需要经历将高级语言代码（HLL）转换为二进制的所有阶。我们只对词法分析和语法分析感兴趣。这两个步骤在代码生成AST中起主要作用。

第一步。词法分析器，也称为扫描仪（scanner），它读取字符流（我们的代码）并使用定义的规则将它们组合成标记（token）。此外，它将删除空格字符，注释等。最后，整个代码字符串将被拆分为一个标记列表。
{% asset_img ast_5.png %}

当词法分析器读取源代码时，它会逐字母地扫描代码; 当它遇到空格，操作符号或特殊符号时，它决定一个单词完成了。

第二步。 语法分析器（也称为解析器）将在词法分析后获取一个简单的标记列表，并将其转换为树形表示，验证语言语法并抛出语法错误（如果发生这种情况）。
{% asset_img ast_6.png %}

在生成树时，一些解析器会省略不必要的标记（例如冗余括号），因此它们创建AST（不是100％ 的代码匹配），但知道如何处理它。另一方面，完全覆盖所有代码结构的解析器生成的树称为“具体语法树（Concrete Syntax Tree， CST）”。
{% asset_img ast_7.png %}

## 想要了解有关编译器的更多信息？
你可以从这个[超微型编译，The-super-tiny-compiler](https://github.com/jamiebuilds/the-super-tiny-compiler) 库开始。它是用JavaScript 编写的所有主要编译器的超简化示例。它有200 行实际代码，其背后的想法是将Lisp 编译成C 语言。所有代码都包含注释和解释。
{% asset_img ast_8.png %}

一个更好的项目[LangSandbox](https://github.com/ftomassetti/LangSandbox)。它说明了如何编写一门编程语言。有文章或书籍列表（如果你愿意）介绍如何做到这一点。所以，它更进一步，因为在这里你可以编写你的语言并编译成C/字节码，并在之后执行，而不是将Lisp 编译为C（就像在前面的例子中一样）。
{% asset_img ast_9.png %}

可以使用库吗？当然，有很多库。你可以访问[astexplorer](https://astexplorer.net/)并选择你喜欢的一个。有一个实时编辑器（live editor），你可以在其中使用AST 解析器。除了JavaScript 之外，它还包含许多其他语言。
{% asset_img ast_10.png %}

我想特别强调其中一个，称为Babylon。
{% asset_img ast_11.png %}

它用于Babel，也许这是它受欢迎的原因。因为它是由Babel 项目支持的，所以你可以期待它将始终与最新的JS 特性保持同步，在过去几年中经常这样做。所以，当我们接下来的事情就像'异步迭代（asynchronouse iteration）'时，这个解析器不会告诉你'意外的令牌（Unexpected token）'。此外，它具有相当好的API 并且通常易于使用。

现在你知道如何为代码生成AST。接下来是继续讨论现实中的用例。

第一个用例是代码转换，也就是Babel。
>Babel is not a ‘tool for having ES6 support’. Well, it is, but it is far not only what it is about.

Babel不是“使ES6 工具得到支持的工具”。这不是它的全部意义。

许多人将Babel 与ES6/7/8 特性的支持联系在一起。事实上，这就是我们经常使用它的原因。但它只是一组插件。我们也可以将它用于代码缩小，与React 相关的语法转换（例如JSX），Flow 的插件等。
{% asset_img ast_12.png %}


Babel 是一个JS 编译器。在较高的层次上，运行代码分为3 个阶段：解析，转换和生成。Babel 接受JavaScript 代码，修改代码并生成新的代码返回。它如何修改代码？准确说，它构建AST，遍历它，根据应用的插件修改它，然后从修改的AST 生成新代码。

让我们在一个简单的代码示例中看一下。
{% asset_img ast_13.png %}

正如之前提到的，Babel 使用Babylon，因此，我们首先解析代码，然后遍历AST并反转所有变量名称。最后一步--生成代码。完成。正如你在这里看到的那样，第一步（解析）和第三部（代码生成）阶段看起来很常见，这就是你每次都要做的事情。所以，Babel 会处理它们，因为你唯一真正感兴趣的是AST 转换。

在开发Babel-plugin 时，你只需描述将改变AST 的节点“visitor”。
{% asset_img ast_14.png %}

将它添加到Babel 插件列表中，在webpack 配置中设置babel-loader，在这里我们可以轻松看到它们。

如果想了解更多关于如何构建一个babel-plugin 的信息，可以查看[Babel-handbook](https://github.com/jamiebuilds/babel-handbook)。
{% asset_img ast_15.png %}

接下来，我想提到的下一个用例是自动代码重构和[JSCodeshift](https://github.com/facebook/jscodeshift)。

假设你希望用简短的箭头函数替换所有旧的匿名函数。
{% asset_img ast_16.png %}

代码编辑器很可能无法做到这一点，因为它不是简单的查找替换操作。这就是jscodeshift 发挥作用的地方。

你很可能会与codemods 一起听到jscodeshift，这会让你感到困惑。Jscodeshift 是一个用于运行codemods 的工具包。codemods 是一个实际描述应该对AST 进行哪种转换的代码。所以，这个想法与Babel 及其插件非常相似。
{% asset_img ast_17.png %}

可以看出它同Babel 插件非常像。

因此，如果想创建“自动化方式（automated way）”来将代码库迁移到新的框架版本，那么这是一种方法。例如，这里是react 16个prop types 重构。
{% asset_img ast_18.png %}

希望所有人都已经迁移到React 16 版本了;)

尝试一下，已有创建的许多差异codemodes，你可以节省一些时间。
{% asset_img ast_19.png %}

稍微提及的最后一个用例是Prettier，因为可能每个人日常工作中都会使用它。
{% asset_img ast_20.png %}

Prettier 格式化我们的代码。它会打破长代码，清理空格，括号等。因此它将代码作为输入并将修改后的代码作为输出。听起来很熟悉吧？
{% asset_img ast_21.png %}

这个想法仍然是一样的。首先，获取代码并生成AST。然后，实际上prettier 的魔法将会发生。AST 将转换为“中间代表（intermediate representation）”或“Doc”。 在更高级别，AST 节点将扩展信息，它们在格式化方面如何相互关联，例如，函数的参数列表应被视为一组项目。因此，如果列表很长并且不适合一行，则将每个参数分成单独的行等。然后，主算法，称为“打印机”将通过IR 并基于整个图片决定如何格式化代码。

再说一次，如果你想了解漂亮印刷背后的更多理论，你可以参考这本书[A prettier printer](http://homepages.inf.ed.ac.uk/wadler/papers/prettier/prettier.pdf)。
{% asset_img ast_22.png %}

要提到的最后一件事是我的[js2flowchart](https://github.com/Bogdan-Lyashenko/js-code-to-svg-flowchart)（Github上的4.2k星）。
{% asset_img ast_23.png %}

它将JS 代码生成SVG 流程图。

这是一个很好的例子，因为它告诉你，当你有AST 代码表示时，你可以做任何你想做的事情。没有必要将它返回到代码字符串，你可以绘制它的流程图等等。

用例是什么？你可以通过流程图解释/记录代码，通过视觉理解学习其它代码，为有效JS 语法简单描述的任何流程创建流程图。

最简单的尝试方法--转到[live editor](https://bogdan-lyashenko.github.io/js-code-to-svg-flowchart/docs/live-editor/index.html)。
{% asset_img ast_24.png %}

试试看。此外，你可以从代码中使用它，或者它也有CLI，因此你只需指向要生成SVG 文件的文件。此外，还有VS Code扩展（链接 在readme 中）。

那么，它还能做些什么呢？好吧，首先，除了生成所有代码的大方案之外，你还可以指定抽象级别。
{% asset_img ast_25.png %}

它意味着你只绘制模块导出，或仅绘制类定义，或函数定义及其调用。然后，你可以生成演示文稿，其中每张幻灯片都深入细节。

还有一堆好用的工具可以修改树。例如，你可以在这里看到'.forEach'方法调用，它只是方法调用，但是我们可以指定它们应该被视为循环，因为我们知道forEach 是一个循环，所以，让我们像一个循环渲染它。

{% asset_img ast_26.png %}

好吧，它是如何工作的？
{% asset_img ast_27.png %}

首先，将代码解析为AST，然后，我们遍历AST 并生成另一棵树，我称之为FlowTree（FT）。它省略了许多小的，不重要的令牌，但将关键块放在一起，如函数，循环，条件等。之后，我们遍历FlowTree 并从中创建ShapesTree（ST）。 ShapesTree 的每个节点都包含有关其视觉类型，定位，树中连接等的信息。最后一步，我们遍历所有形状，并为每个形状生成SVG 表示，将所有形状组合成一个SVG 文件。

看看吧[https://github.com/Bogdan-Lyashenko/js-code-to-svg-flowchart](https://github.com/Bogdan-Lyashenko/js-code-to-svg-flowchart)

## 相关链接
1.[https://www.slideshare.net/BohdanLiashenko/ast-for-javascript-developers](https://www.slideshare.net/BohdanLiashenko/ast-for-javascript-developers)
2.[超微型编译，The-super-tiny-compiler](https://github.com/jamiebuilds/the-super-tiny-compiler)
3.[LangSandbox](https://github.com/ftomassetti/LangSandbox)
4.[astexplorer](https://astexplorer.net/)
5.[Babel-handbook](https://github.com/jamiebuilds/babel-handbook)
6.[JSCodeshift](https://github.com/facebook/jscodeshift)
7.[A prettier printer](http://homepages.inf.ed.ac.uk/wadler/papers/prettier/prettier.pdf)
8.[js2flowchart](https://github.com/Bogdan-Lyashenko/js-code-to-svg-flowchart)
