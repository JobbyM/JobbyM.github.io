---
title: 介绍npx：npm 包运行器
date: 2018-08-26 13:32:52
comments: true
tags:
  - 技术
  - npm
  - npx
  - 翻译
categories: 技术
---

> 子曰：介绍npx：npm 包运行器

关于本文：
原文：https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b [俄语版](https://medium.com/devschacht/introducing-npx-an-npm-package-runner-a72a658cd9e6)
作者：@[Kat Marchán](https://medium.com/@maybekatz?source=post_header_lockup)

将npm 升级到最新版本[npm@5.2.0](https://github.com/npm/npm/releases/tag/v5.2.0) 的人可能会注意到它会在通常的`npm` 旁边安装了一个新的二进制文件：[npx](https://npm.im/npx)。

npx 是一个工具，旨在帮助完善使用npm 注册表中的包的体验 -- 同样地，npm 使得安装和管理托管在注册表上的依赖项变得非常容易，npx 使得使用CLI 工具和托管在注册表上的其他可执行文件变得容易。它大大简化了许多事情，直到现在，还需要一些普通npm 的仪式：

<!--more-->

## 使用本地安装的工具而不需要使用npm run-script（Using locally-installed tools without npm run-script）
{% asset_img npx_0.gif %}
将cowsay 安装为本地devDependency 并使用`$ npx cowsay`运行它

在过去的几年中，npm 生态系统越来越倾向于将工具安装为项目本地`devDependencies`，而不是要求用户在全局范围内安装它们。这意味着像[mocha](https://npm.im/mocha)，[grunt](https://npm.im/grunt-cli) 和[bower](https://npm.im/bower) 这样曾经主要全局安装在系统上的工具现在可以在每个项目的基础上管理它们的版本。这也意味着，要使基于npm 的项目启动并运行，你需要做的就是确保在系统上有node+npm，克隆git repo，然后执行`npm it` 去运行`npm install` 和`npm test`。由于`npm run-script` 将本地二进制文件添加到路径，这样就可以了！

缺点是，这使你无法以交互方式快速、方便地调用本地二进制文件。有几种方法可以做到这一点，他们都对他们有一些烦恼：你可以将这些工具添加到你的`scripts`，但是你需要记住通过使用`--` 来传递参数，你可以做使用shell 脚本技巧`alias npmx=PATH=$(npm bin):$PATH`，或者你可以使用`./node_modules/.bin/mocha` 手动路径到它们。这些都有效，但没有一个是理想的。

npx 为你提供了我认为最好的解决方案：`$ npx mocha` 是你使用本地安装所需的全部工作。如果你采取额外的步骤并配置[shell自动回退](https://www.npmjs.com/package/npx#shell-auto-fallback)（下面有更多内容），那么项目目录中的`$ mocha` 将为你提供帮助！

好处是，如果调用已经安装的二进制文件，[npx 基本上没有开销](https://twitter.com/maybekatz/status/877444832494596096) -- 它足够聪明，可以将工具的代码直接加载到当前运行的`node` 进程中！这与此类事情的速度差不多，并使其成为完全可以接受的脚本编写工具。

## 执行一次性命令（Executing one-off commands）
{% asset_img npx_1.gif %}
`$ npx create-react-app my-cool-new-app` 安装一个临时的`create-react-app` 并调用它，不会污染全局安装或需要多个步骤！

你是否曾经遇到过想要尝试某种CLI 工具的情况，但是为了运行它必需全局安装，这很烦人吗？ npx 能很好处理这个问题。当`<command>` 不在`$PATH` 中时调用`npx <command>` 将自动从npm 注册表中安装具有该名称的包，并调用它。完成后，安装的软件包将不会出现在全局文件中的任何位置，因此不必长期担心污染问题。

此功能也适用于生成器等。像[yeoman](https://npm.im/yo) 或[create-react-app](https://npm.im/create-react-app) 这样的工具只调用一次。 **当你再次运行它们时，它们已经已经过时了，所以每次你想要使用它们时最终都要运行安装**。

作为一个工具维护者，我非常喜欢这个功能，因为这意味着我可以将`$ npx my-tool` 放入`README.md` 介绍中，而不是试图让人们处理实际安装它的障碍。坦率地说，“哦，只是复制粘贴这一个命令，它是零承诺”对于不确定是否使用工具的用户来说更加可口。

这里有一些其他有趣的软件包，你可能想尝试使用npx：[happy-birthday](https://npm.im/happy-birthday)，[benny-hill](https://npm.im/benny-hill)，[workin-hard](https://npm.im/workin-hard)，[cowsay](https://npm.im/cowsay)，[yo](https://npm.im/yo)，[create-react-app](https://npm.im/create-react-app)，[npm-check](https://npm.im/npm-check)。甚至还有一个[完整的awesome-npx 仓库](https://github.com/js-n/awesome-npx)！前进！[一个完整的本地REST服务器运行的](https://twitter.com/maybekatz/status/878926190064668672)命令足够小，可以放在推文中。

## 运行不同Node.js版本的命令（Run commands with different Node.js versions）
{% asset_img npx_2.png %}
`npx -p node @ <version> node -v`可用于执行Node 版本的一次性运行。

事实证明，[Aria Stewart](https://medium.com/@aredridel) 的这个很酷的软件包在npm 注册表中称为[node](https://npm.im/node)。 这意味着你可以非常轻松地使用不同的node 版本尝试node 命令，而无需使用像[nvm](http://nvm.sh/)，[nave](https://npm.im/nave) 或[n](https://npm.im/n) 这样的版本管理器。你只需要一个`npm@5.2.0` 安装！

npx 的`-p` 选项允许你指定要安装的软件包并添加到正在运行的`$PATH` 中，这意味着你可以执行有趣的操作，例如：`$ npx -p node@6 npm it` 可以安装并测试当前的npm 软件包就像你在全局运行`node@6`。我自己一直使用它--甚至最近不得不在一个项目中使用它，因为我的一个测试库在`node@8` 下执行。这是一个真正的生命保护程序，我发现这种用例比版本管理器更容易使用，我总是以某种方式找到破解或错误配置的方法。

## 以交互方式开发npm run-scripts（Developing npm run-scripts interactively）
{% asset_img npx_3.gif %}
`$ npx -p cowsay -p lolcatjs -c'echo“$ npm_package_name @ $ npm_package_version”| cowsay | lolcatjs'`安装cowsay 和lolcatjs，并使脚本可以从运行脚本访问`$ npm_`变量池。

现在很多npm 用户都利用了非常酷的[`run-script`功能](https://docs.npmjs.com/misc/scripts)。他们不仅赋值你的`$PATH`，以便可以访问本地二进制文件，而且还添加了一大堆你可以在这些脚本中访问的环境变量！你可以使用`$ npm run env | grep npm_` 查看这些额外变量 。

这可能会使开发和测试运行脚本变得棘手--这意味着即使使用`$ (npm bin)/some-bin` 等技巧，你仍然无法在交互式工作时访问这些神奇的环境变量。

可是等等！npx 还有另一个技巧：当你使用`-c` 选项时，在字符串参数内写的脚本将具有与常规运行脚本相同的env 变量的完全访问权限！你甚至可以通过单个npx 调用使用管道和多个命令！

## 与亲人朋友分享基于gist 的脚本！（Share gits-based scripts with friends and loved ones!）
{% asset_img npx_4.gif %}

使用[gist.github.com](https://gist.github.com/) 分享各种实用程序脚本，而不是设置整个git repos，发布新工具等，这已经变得非常普遍。

使用npx，你可以更进一步：因为npx 接受npm 本身所做的任何说明，你可以创建一个人们可以用一个命令直接调用的gist！

请自行试用[https://gist.github.com/zkat/4bc19503fe9e9309e2bfaa2c58074d32](https://gist.github.com/zkat/4bc19503fe9e9309e2bfaa2c58074d32)！

注意：保持安全！在执行这些时，请务必仔细阅读gists，就像运行`.sh` 脚本时一样！

## 奖金环节：shell自动回退（Bonus Round: shell auto-fallback）
{% asset_img npx_5.png %}
将npx 自动回退放在.zshrc 中意味着你可以在不引用npx 的情况下执行`$ ember-cli @ latest ...`！

由[FélixSaparelli](https://medium.com/@passcod) 添加的这个非常棒的功能意味着对于许多类似用例，你甚至不需要直接调用`npx`！常规npx 使用和回退之间的主要区别在于，除非使用`pkg@version` 语法，否则回退不会安装新软件包：防止潜在危险的错误输入的安全网。

设置自动回退非常简单：在npx 文档中查找[用于当前shell 的命令](https://www.npmjs.com/package/npx#for-bash)，将其添加到`.bashrc`/`.zshrc`/`.fishrc`，然后重新启动shell（或使用`source` 或其他一些机制进行刷新shell）。

现在，你可以尝试执行`$ standard@8 --version` 之类的不同版本的东西，如果你在npm 项目中，`$ mocha` 将自动回退到本地安装的mocha 版本，前提是它不是已在全局安装。

## 使用它！（Do It Live!）
你现在可以通过安装`npm@5.2.0` 或更高版本来获得npx --或者，如果你不想使用npm，你可以安装[独立版本的npx](https://npm.im/npx)！它与其他软件包管理器完全兼容，因为任何npm 使用仅用于内部操作。哦，它有[10 种不同的语言版本](https://github.com/zkat/npx/tree/latest/locales)，这得益于[来自世界各地的一群早期使用者的贡献](https://github.com/zkat/npx/pulls?q=is%3Apr+is%3Aclosed+label%3Ai18n)，其中`--help` 和所有系统消息都已翻译并根据系统区域自动提供！还有[一个很棒的npx repo](https://github.com/js-n/awesome-npx)，里面有一些与npx 配合使用的例子！

你有喜欢的功能吗？你有没有用过它？如果你有一些很酷的东西要炫耀但我没有在这里列出，请在评论中分享！我很想听听其他人的意见！

附：人们不断问这个问题所以我只能贴在这里：在这些GIF 中，我使用[Hyper](https://hyper.is/) 和[monokai 主题](https://www.npmjs.com/package/hyperterm-monokai) 运行[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) 的[太空船主题](https://www.npmjs.com/package/hyperterm-monokai)，加上[FiraCode字体](https://github.com/tonsky/FiraCode)。
