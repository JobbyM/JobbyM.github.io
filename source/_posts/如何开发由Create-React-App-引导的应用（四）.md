---
title: 如何开发由Create-React-App 引导的应用（四）
date: 2017-04-28 10:09:14
comments: true
tags:
  - 技术
  - Create React App
  - 翻译
categories: 技术
---

> 此文章是翻译[How to develop apps bootstrapped with Create React App](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md) 官方文档

## 系列文章

1. {% post_link 如何开发由Create-React-App-引导的应用  如何开发由Create-React-App 引导的应用 %}
2. {% post_link 如何开发由Create-React-App-引导的应用（一） 如何开发由Create-React-App 引导的应用（一）%}
3. {% post_link 如何开发由Create-React-App-引导的应用（二） 如何开发由Create-React-App 引导的应用（二）%}
4. {% post_link 如何开发由Create-React-App-引导的应用（三） 如何开发由Create-React-App 引导的应用（三）%}

## Advanced Configuration

你可以通过在shell中设置环境变量或[.env](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-development-environment-variables-in-env)来调整各种开发和生产设置。

Variable | Development | Production | Usage
:--- | :---: | :---: | :---
BROWSER | {% asset_img white_check_mark.png %} | {% asset_img x.png %} | 默认情况下，Create React App 将打开默认系统浏览器，在Mac OS上支持Chrome。 指定[浏览器](https://github.com/sindresorhus/opn#app)来覆盖此行为，或将其设置为`none`以完全禁用它。
HOST | {% asset_img white_check_mark.png %} | {% asset_img x.png %} | 默认情况下，开发Web服务器绑定到`localhost`。 你可以使用此变量来指定不同的主机。
PORT | {% asset_img white_check_mark.png %} | {% asset_img x.png %} | 默认情况下，开发Web服务器将尝试在端口3000上侦听或提示你尝试下一个可用端口。 你可以使用此变量来指定不同的端口。
HTTPS | {% asset_img white_check_mark.png %} | {% asset_img x.png %} | 当设置为 `true` 时, Create React App 将以`https` 模式运行开发Web服务器。
PUBLIC_URL | {% asset_img x.png %} | {% asset_img white_check_mark.png %} | Create React App 假定你的应用程序托管在Web服务器的根目录或在[`package.json`（`homepage`）](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#building-for-relative-paths)中指定的子路径。 通常，Create React App 忽略hostname。 你可以使用此变量强制将资源逐字引用到你提供的URL（包含hostname）。 当使用CDN托管你的应用程序时，这可能特别有用。
CI | {% asset_img large_orange_diamond.png %} | {% asset_img white_check_mark.png %} | 当设置为`true` 时, Create React App 将警告视为构建中的失败。 它也使test runner 处于非观察者模式. 大多数CI 默认设置此标志。

<!--more-->

## Troubleshooting

### `npm start` doesn't  detect changes

当`npm start`运行时保存文件，浏览器将刷新更新的代码。
如果没有发生这种情况，请尝试以下解决方法之一：

* 如果你的项目在Dropbox文件夹中，请尝试将其移出。
* 如果观察者看不到名为`index.js`的文件，并且你通过文件夹名称引用它，则[需要重新启动观察者](https://github.com/facebookincubator/create-react-app/issues/1164)，这是Webpack错误导致的。
* 一些像Vim和IntelliJ这样的编辑器有一个“安全写入”功能，目前会打断观察者。你需要禁用它。按照[“使用支持安全写入的编辑器”](https://webpack.github.io/docs/webpack-dev-server.html#working-with-editors-ides-supporting-safe-write)中的说明进行操作。
* 如果你的项目路径包含括号，请尝试将项目移动到没有它们的路径。这是由[Webpack观察者错误](https://github.com/webpack/watchpack/issues/42)引起的。
* 在Linux和macOS上，你可能需要[调整系统设置](https://webpack.github.io/docs/troubleshooting.html#not-enough-watchers)以允许更多观察者。
* 如果项目在虚拟机中运行，如虚拟机（Virtual Vagrant），则在项目目录中创建一个`.env`文件，如果它不存在，并向其添加`CHOKIDAR_USEPOLLING=true`。这样可以确保下次运行`npm start`时，观察者会根据需要在VM中使用轮询模式。

如果这些解决方案没有帮助，请在[此主题中](https://github.com/facebookincubator/create-react-app/issues/659)留下评论。

### `npm test` hangs on macOS Sierra

如果你运行`npm test`，并且控制台在打印`react-scripts test --env=jsdom`之后卡住，则可能是你的[Watchman](https://facebook.github.io/watchman/)安装问题，如[facebookincubator/create-react-app＃713]()https://github.com/facebookincubator/create-react-app/issues/713中所述。

我们建议你先删除项目中的`node_modules`并运行`npm install`（或者`yarn`如果你使用的话）。 如果没有帮助，你可以尝试以下问题中提到的许多解决方法之一：

* [facebook/jest#1767](https://github.com/facebook/jest/issues/1767)
* [facebook/watchman#358](https://github.com/facebook/watchman/issues/358)
* [ember-cli/ember-cli#6259](https://github.com/ember-cli/ember-cli/issues/6259)

据悉，安装Watchman 4.7.0或更新版本可以解决此问题。 如果你使用[Homebrew](http://brew.sh/)，则可以运行这些命令来更新它们：
```sh
watchman shutdown-server
brew update
brew reinstall watchman
```

你可以在Watchman文档页面上找到[其他安装方法](https://facebook.github.io/watchman/docs/install.html#build-install)。

如果仍然没有帮助，请尝试运行`launchctl unload -F ~/Library/LaunchAgents/com.github.facebook.watchman.plist`。

还有报告说，卸载Watchman可以解决问题。 所以如果没有其他帮助，请从系统中删除它，然后重试。

### `npm run build` silently fails

据报道，在没有swap space的机器上，`npm run build` 可能会失败，这在云环境中很常见。 如果[症状匹配](https://github.com/facebookincubator/create-react-app/issues/1133#issuecomment-264612171)，请考虑为构建的机器添加一些swap space，或在本地构建项目。

### `npm run build` fails on Heroku

这可能是文件名大小写敏感的问题。 请参阅[本节](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#resolving-heroku-deployment-errors)。

## Someting Missing?

如果你在这个页面上有更多的"How To" 的想法，请[让我们知道](https://github.com/facebookincubator/create-react-app/issues)或[贡献一些！](https://github.com/facebookincubator/create-react-app/edit/master/packages/react-scripts/template/README.md)
