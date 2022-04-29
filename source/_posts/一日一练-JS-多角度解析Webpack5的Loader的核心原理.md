---
title: 一日一练-JS 多角度解析Webpack5的Loader的核心原理
date: 2022-02-18 18:01:23
tags:
  - 技术
  - 一日一练
  - Webpack
  - Loader
  - 转载
categories: 技术
---

本文转载自[稀土掘金 多角度解析Webpack5的Loader的核心原理](https://juejin.cn/post/7036379350710616078/)，有部分删节，看原文请到原地址。

## 前言
日益繁杂的前端工程化中，围绕 `Webpack` 的前端工程化在前端项目中显得格外重要, 谈到 `webpack` 必不可少的就会提起 `Loader` 机制。

这里我们会从应用-原理-实现一层一层来揭开 `loader` 的面目。废话不多说，让我们快速开始吧。

文章会围绕以下三个方面循序渐进带你彻底掌握 `Webpack Loader` 机制:

* **`Loader` 概念**: 何谓 `Loader`, 从基础出发带你快速入门日常业务中 `Loader` 的各种配置方式。
* **`Loader` 原理**: 从源码解读 `Loader` 模块，手把手实现 `Webpack` 核心`loader-runner` 库。
* **`Loader` 实现**: 复刻高频次出现的 `Babel-loader`，带你掌握企业级 `Loader` 开发流程。

<!--more-->

这里我们会告别枯燥的源码阅读方式，图文并茂的带大家掌握 `Loader` 核心原理并且熟练应用于各种场景之下
## 
> 文章基于 `webpack` 最新 `5.64.1` 版本 `loader-runner4.2.0` 版本进行分析。
<!--more-->

## `Loader` 概念
### `Loader` 的作用
让我们先从最基础的开始说起，所谓 `Loader` 本质上就是一个函数。

> `loader` 用于对模块的源代码进行转换。`loader` 可以使你在 `import` 或 `"load(加载)"` 模块时预处理文件。因此，`loader` 类似于其他构建工具中 `“任务(task)”`，并提供了处理前端构建步骤的得力方式。`loader` 可以将文件从不同的语言（如 `TypeScript`）转换为 `JavaScript` 或将内联图像转换为 `data URL`。`loader` 甚至允许你直接在 `JavaScript` 模块中 `import CSS` 文件！

`webpack` 中通过 `compilation` 对象进行模块编译时，会首先进行匹配 `loader` 处理文件得到结果(`string/buffer`),之后才会输出给 `webpack` 进行编译。

简单来说，**`loader` 就是一个函数，通过它我们可以在 `webpack` 处理我们的特定资源(文件)之前进行提前处理**。

比方说，`webpack` 仅仅只能识别 `javascript` 模块，而我们在使用 `TypeScript` 编写代码时可以提前通过 `babel-loader` 将 `.ts` 后缀文件提前编译成为 `JavaScript` 代码，之后再交给 `Webapack` 处理。

### `Loader` 配置相关 `API`
#### 常用基础配置参数
我们来看一段最简单的 `webpack` 配置文件:
```js
module.exports = {
    module: {
        rules: [
            { 
                test: /\.css$/,
                use: 'css-loader',
                enforce: 'post'
            },
            {
                test: /\.ts$/,
                use: 'ts-loader'
            }
        ]
    }
}
```

相信这段配置代码大家已经耳熟能详了，我们通过 `module` 中的 `rules` 属性来配置 `loader`。

其中:

* `test` 参数
`test` 是一个正则表达式，我们会对应的资源文件根据 `test` 的规则去匹配。如果匹配到，那么该文件就会交给对应的 `loader` 去处理。

* `use` 参数
`use` 表示匹配到 `test` 中匹配对应的文件应该使用哪个 `loader` 的规则去处理，`use` 可以为一个字符串，也可以为一个数组。

> 额外注意，如果 `use` 为一个数组时表示有多个 `loader` 依次处理匹配的资源，按照 **从右往左(从下往上)** 的顺序去处理。

* `enforce` 参数
`loader` 中存在一个 `enforce` 参数标志这 `loader` 的顺序,比如这样一份配置文件:
```js
module.exports = {
  module: {
    rules: [
      { test: /\.css$/, use: 'sass-loader', enforce: 'pre' },
      { test: /\.css$/, use: 'css-loader' },
      { test: /\.css$/, use: 'style-loader', enforce: 'post' },
    ],
  },
};
```

针对 `.css` 结尾的资源文件，我们在打包过程中 `module.rules` 分别有三条规则匹配到，也就是对于同一个 `.css` 文件我们需要使用匹配到的三个 `loader` 分别进行处理。

**那么此时，如果我们希望三个 `loader` 的顺序可以不根据书写时的顺序去处理，那么`enforce` 就会大显身手。**

`enforce` 有两个值分别为 `pre`、`post`。

* 当我们的 `rules` 中的规则没有配置 `enforce` 参数时,默认为 `normal loader`(默认 `loader`)。
* 当我们的 `rules` 中的规则配置 `enforce:'pre'` 参数时,我们称之它为 `pre loader`(前置 `loader`)。
* 当我们的 `rules` 中的规则配置 `enforce:'post'` 参数时,我们称之它为 `post loader` (后置 `loader`)。

关于这三种 `loader` 的执行顺序，我想大家根据名称也可以猜的出来一二，没错在 **正常** `loader` 的执行阶段这三种类型的 `loader` 执行顺序为:
{% asset_img 1.webp %}

#### `webpack` 中配置 `loader` 的三种方式
通常我们在配置时都是直接使用直接使用 `loader` 名称的方式，比如:
```js
// webpack.config.js
module.exports = {
    ...
    module: {
        rules: [
            {
                test:/\.js$/,
                loader: 'babel-loader'
            }
        ]
    }
}
```

上边的配置文件中，相当于告诉 `webpack` 关于 `js` 结尾的文件使用 `babel-loader` 去处理。**可是这里我们明明只写了一个 `babel-loader` 的字符串，它是如何去寻找到 `babel-loader` 的真实内容呢？**

带着这个疑问，接下来让我们一起来看看在 `webpack` 中配置 `loader` 的三种方式。

* **绝对路径**
第一种方式在项目内部存在一些未发布的自定义 `loader` 时比较常见，**直接使用绝对路径地址的形式指向 `loader` 文件所在的地址**。 比如:
```js
const path = require('path')
// webpack.config.js
module.exports = {
    ...
    module: {
        rules: [
            {
                test:/\.js$/,
                // .js后缀其实可以省略，后续我们会为大家说明这里如何配置loader的模块查找规则
                loader: path.resolve(__dirname,'../loaders/babel-loader.js')
            }
        ]
    }
}
```

* **`resolveLoader.alias`**
第二种方式我们可以通过 `webpack` 中的 `resolveLoader` 的别名 `alias` 方式进行配置，比如:
```js
const path = require('path')
// webpack.config.js
module.exports = {
    ...
    resolveLoader: {
        alias: {
            'babel-loader': path.resolve(__dirname,'../loaders/babel-loader.js')
        }
    },
    module: {
        rules: [
            {
                test:/\.js$/,
                loader: 'babel-loader'
            }
        ]
    }
}
```

此时，当 `webpack` 在解析到 `loader` 中使用 `babel-loader` 时，查找到 `alias` 中定义了 `babel-loader` 的文件路径。就会按照这个路径查找到对应的 `loader` 文件从而使用该文件进行处理。

当然在我们定义 `loader` 时如果每一个 `loader` 都需要定义一次 `resolveLoader.alias` 的话无疑太过于冗余了，情况在真实业务场景下通常我们都很少自己定义`resolveLoader` 选项但是 `webpack` 也可以自动的帮我们找到对应的 `loader`，这就要引出我们的另一个参数了。

* **`resolveLoader.modules`**
我们可以通过 `resolveLoader.modules` 定义 `webpack` 在解析 `loader` 时应该查找的目录，比如:
```js
const path = require('path')
// webpack.config.js
module.exports = {
    ...
    resolveLoader: {
        modules: [ path.resolve(__dirname,'../loaders/') ]
    },
    module: {
        rules: [
            {
                test:/\.js$/,
                loader: 'babel-loader'
            }
        ]
    }
}
```

上述代码中我们将 `resolveLoader.modules` 配置为 `path.resolve(__dirname,'../loaders/')`，此时在 `webpack` 解析 `loader` 模块规则时就会去 `path.resolve(__dirname,'../loaders/')` 目录下去寻找对应文件。

当然 `resolveLoader.modules` 的默认值是 `['node_modules']`，自然在默认业务场景中我们通过 `npm install` 按照的第三方 `loader` 都是存在于 `node_modules` 内所以配置 `mainFields` 默认就可以找到对应的 `loader` 入口文件。

> 关于 `resolveLoader` 有些同学可能户非常眼熟，它和 `resolve` 正常模块解析的配置参数是一模一样的。只不过 `resolveLoader` 是相对于 `loader` 的模块加载规则的，具体更多的配置手册[你可以在这里看到](https://webpack.js.org/configuration/resolve/#resolveloader)。

同时需要注意的是 `modules` 字段中的相对路径查找规则是类似于 `Node` 查找 `'node_modules'` 的方式进行查找。比如说 `modules:['node_modules']`，即是在当前目录中通过查看当前目录以及祖先路径（即 `./node_modules`, `../node_modules` 等等）进行规则查找。

#### `loader` 种类与执行顺序

* **`Loader` 的种类**
上边我们讲到了通过配置文件的 `enforce` 参数可以将 `loader` 分为三种类型:`pre loader`、`normal loader`、`post noraml`,分别代表了三种不同的执行顺序。

当然在 **配置文件中根据 `loader` 的执行顺序，我们可以将 `loader` 分为三种类型，同时 `webpack` 还支持一种内联的方式配置 `loader`**, 比如我们在引用资源文件时:
```js
import Styles from 'style-loader!css-loader?modules!./styles.css';
```

通过上述的方式，我们在引用 `./styles.css` 时候，调用了 `css-loader`、`style-loader` 进行提前处理文件，同时给 `css-loader` 传递了 `modules` 的参数。

我们将引用资源时，通过 `!` 分割使用 `loader` 的方式称为**行内** `loader`。

至此，我们清楚关于 `loader` 的种类存在四种类型的 `loader`,分别是 `pre loader`、`normal loader`、`inline loader`、`post loader` 四种类型。

关于 `inline loader` 还有一些**特殊的前置参数需要大家清楚:**

通过为内联 `import` 语句添加前缀，可以覆盖配置中的所有 `normalLoader`, `preLoader` 和 `postLoader`：

* 使用 `!` 前缀，将禁用所有已配置的 `normal loader`(普通 `loader`)
```js
import Styles from '!style-loader!css-loader?modules!./styles.css';
```

* 使用 `!!` 前缀，将禁用所有已配置的 `loader`（`preLoader`, `loader`, `postLoader`）
```js
import Styles from '!!style-loader!css-loader?modules!./styles.css';
```

* 使用 `-!` 前缀，将禁用所有已配置的 `preLoader` 和 `loader`，但是不禁用 `postLoaders`
```js
import Styles from '-!style-loader!css-loader?modules!./styles.css';
```

> 这里大家没有死记硬背的必要，了解大致用法后具体可以通过 [`webpack` 官方网站](https://webpack.js.org/concepts/loaders/#inline)进行查阅即可。

* **`Loader` 的执行顺序**
在了解了我们将 `loader` 分为了`pre loader`、`normal loader`、`inline loader`、`post loader` 四种 `loader`。

其实这四种 `loader` 通过命名我们也可以看出来他们的执行顺序，在**默认的 `Loader` 执行阶段**这四种 `loader` 会按照如下顺序执行:
{% asset_img 2.webp %}

在 `webpack` 进行编译文件前，资源文件匹配到对应 `loader`:

* 执行 `pre loader` 前置处理文件。

* 将 `pre loader` 执行后的资源链式传递给 `normal loader` 正常的 `loader`处理。

* `normal loader` 处理结束后交给 `inline loader` 处理。

* 最终通过 `post loader` 处理文件，将处理后的结果交给 `webpack` 进行模块编译。

> 注意这里我们强调的是默认 `loader` 的执行阶段，那么什么是非默认呢？接下来让我们一起来看看所谓的 `pitch loader` 阶段。


* **`Loader` 的 `pitch` 阶段**
关于 `loader` 的执行阶段其实分为两种阶段:

* 在处理资源文件之前，首先会经历 `pitch` 阶段。
* `pitch` 结束后，读取资源文件内容。
* 经过 `pitch` 处理后，读取到了资源文件，此时才会将读取到的资源文件内容交给正常阶段的 `loader` 进行处理。

简单来说就是所谓的 `loader` 在处理文件资源时分为两个阶段: `pitch` 阶段和`nomral` 阶段。

让我们来看这样一个例子:
```js
// webpack.config.js

module.exports = {
  module: {
    rules: [
      // 普通loader
      {
        test: /\.js$/,
        use: ['normal1-loader', 'normal2-loader'],
      },
      // 前置loader
      {
        test: /\.js$/,
        use: ['pre1-loader', 'pre2-loader'],
        enforce: 'pre',
      },
      // 后置loader
      {
        test: /\.js$/,
        use: ['post1-loader', 'post2-loader'],
        enforce: 'post',
      },
    ],
  },
};
```

```js
// 入口文件中
import something from 'inline1-loader!inline2-loader!./title.js';
```

这里，我们在 `webpack` 配置文件中对于 `js` 文件配置了三种处理规则 `6` 个`loader`。同时在入口文件引入 `./title.js` 使用了我们之前讲到过的 `inline loader`。

让我们用一张图来描述下所谓 `loader` 的执行顺序:
{% asset_img 3.webp %}

`loader` 的执行阶段实际上分为两个阶段， `webpack` 在使用 `loader` 处理资源时**首先会经过 `loader.pitch` 阶段，`pitch` 阶段结束后才会读取文件而后进行 `normal` 阶段处理**。

* **Pitching** 阶段: `loader` 上的 `pitch` 方法，按照 `后置(post)、行内(inline)、普通(normal)、前置(pre)` 的顺序调用。

* **Normal** 阶段: `loader` 上的 常规方法，按照 `前置(pre)、普通(normal)、行内(inline)、后置(post)` 的顺序调用。

请各位同学牢牢记住上边的 `loader` 执行流程图，之后我们也会详细使用这个流程去带大家实现 `loader-runner` 的源码。

> 关于 `pitch` 阶段有什么作用，`webpack` 为何如此设计 `loader`。别着急，后边的内容慢慢为你解开这些答案


* **`pitch Loader` 的熔断效果**
上边我们通过一张图描述了 `webpack` 中 `loader` 的执行顺序。我们了解到除了正常的 `loader` 执行阶段还额外存在一个 `loader.pitch` 阶段。

`pitch loader` 本质上也是一个函数,比如:
```js
function loader() {
    // 正常的loader执行阶段...
}
loader.pitch = function () {
    // pitch loader
}
```

关于 `pitch loader` 的需要特别注意的就是 `Pitch Loader` 带来的**熔断**效果。

假设我们在上边配置的 `8` 个 `loader` 中，为 `inline1-loader` 添加一个 `pitch` 属性使它拥有 `pitch` 函数，**并且，我们让它的 `pitch` 函数随便返回一个非 `undefined` 的值**。
```js
// inline1-loader normal
function inline1Loader () {
    // dosomething
}
// inline1-loader pitch
inline1Loader.pitch = function () {
    // do something
    return '19Qingfeng'
}
```

这里我们在 `inline1-loader pitch` 阶段返回了一个字符串 `19Qingfeng`，我们上边说到过在 `loader` 的执行阶段是会按照这张图进行执行( `pitch` 阶段全部返回`undefined` 情况下)：
{% asset_img 4.webp %}

**但是一旦在某一个 `loader` 的 `pitch` 阶函数中返回一个非 `undefined` 的值就会发生熔断的效果：**
{% asset_img 5.webp %}

**我们可以看到当我们在 `inline1-loader` 的 `pitch` 函数中返回了一个字符串`19Qingfeng` 时，`loader` 的执行链条会被阻断--立马掉头执行，直接掉头执行上一个已经执行的 `loader` 的 `normal` 阶段并且将 `pitch` 的返回值传递给下一个`normal loader`,简而言之这就是 `loader`的熔断效果**


### `Loader` 开发相关 `API`
上边我们带大家入门了 `loader` 的基础概念和配置用法，我们了解了 `loader` 按照执行阶段分为 `4` 种类型且 ·执行 时分为两个阶段: `pitch、normal` 阶段。

接下来让我们来看一下常见开发 `loader` 相关内容:

#### 关于执行顺序对于 `loader` 开发的影响
这里我特意想和大家强调一下，上边我们说过 `loader` 本质上就是一个函数。
```js
function loader() {
    // ...
}

// pitch 属性是可有可无的
loader.pitch = function () {
    // something
}
```

关于 `loader` 的执行顺序是通过 `webpack` 配置中决定的，换而言之一个 `loader` 到底是 `pre、normal、inline` 还是 `post` 和 `loader` 开发本身是没有任何关系的。

**执行顺序仅仅取决于 `webpack` 应用 `loader` 时的配置(或者引入文件时候添加的前缀)。**

#### 同步 or 异步 `loader`

* **同步 `Loader`**
上边我们罗列的 `loader` 都是同步 `loader`，所谓同步 `loader` 很简单。就是在 `loader` 本身阶段同步处理对应逻辑从而返回对应的值:
```js
// 同步loader
// 关于loader的source参数 我们会在后续详尽讲述到 这里你可以理解为需要处理的文件内容
function loader(source) {
    // ...
    // 一系列同步逻辑 最终函数返回处理后的结果交给下一个阶段
    return source
}

// pitch阶段的同步同理
loader.pitch = function () {
    // 一系列同步操作 函数执行完毕则pitch执行完毕
}
```

> 同步 `loader` 在 `normal` 阶段返回值时可以通过函数内部的 `return` 语句进行返回，同时如果需要返回多个值时也可以通过 `this.callback()` 表示 `loader` 结束传入多个值进行返回，比如 `this.callback(error,value2,...)`,需要注意 `this.callback` 第一个参数一定是表示是否存在错误。具体你可以在[这里](https://webpack.js.org/api/loaders/#thiscallback)进行查看更加详细的用法。

* **异步 `Loader`**
在开发 `loader` 时绝大多数情况下我们是用同步 `loader` 就可以满足我们的要求了，但是往往会存在一些特殊情况。比如我们需要在 `loader` 内部调用一些远程接口或者定时器之类的操作。此时就需要 `loader` 可以等待异步返回结束后才继续执行下一个阶段处理：

将 `loader` 变为异步 `loader` 有两种方式:

**返回 `Promise`**
我们仅仅修改 `loader` 的返回值为一个 `Promise` 就可以将 `loader` 变为异步`loader`，后续步骤会等待返回的 `Promise` 变成 `resolve` 后才会继续执行。
```js
function aysncLoader() {
    // dosomething
    return new Promise((resolve) => {
        setTimeout(() => {
            // resolve的值相当于同步loader的返回值
            resolve('19QingFeng')
        }, 3000)
    })
}
```

**通过 `this.async`**
同样还有另一种方式也是比较常用的异步 `loader` 方式,我们通过在 `loader` 内部调用 `this.async` 函数将 `loader` 变为异步，同时 `this.async` 会返回一个`callback` 的方式。只有当我们调用 `callback` 方法才会继续执行后续阶段处理。
```js
function asyncLoader() {
    const callback = this.async()
    // dosomething

    // 调用callback 告诉loader-runner 异步loader 结束
    callback('19QingFeng')
}
```

> 同样 `loader` 的 `pitch` 阶段也可以通过上述两个方案变成异步 `loader`。

#### `normal loader & pitch loader` 参数详解
* **`Normal Loader`**
`normal loader` 默认接受一个参数，这个参数是需要处理的文件内容。在存在多个`loader` 时，它的参数会受上一个 `loader` 的影响。

同时 `nomral loader` 存在一个返回值,这个返回值会链式调用给下一个 `loader` 作为入参，当最后一个 `loader` 处理完成后，会讲这个返回值返回给 `webpack` 进行编译
```js
// source为需要处理的源文件内容 
function loader(source) {
    // ...
    // 同时返回本次处理后的内容
    return source + 'hello !'
}
```

> 关于 `normal loader` 中其实有非常多的属性会挂载在函数中的 `this` 上，比如通常我们在使用某个 `loader` 时会在外部传递一些参数，此时就可以在函数内部通过`this.getOptions()` 方法获取

> 关于 `loader` 中的 `this` 被称作上下文对象，[更多的属性你可以在这里看到](https://webpack.docschina.org/api/loaders/#the-loader-context)

{% asset_img 6.webp %}

* **`Pitch Loader`**
```js
// normal loader
function loader(source) {
    // ...
    return source
}

// pitch loader
loader.pitch = function (remainingRequest,previousRequest,data) {
    // ...
}
```

`Loader` 的 `Pitch` 阶段也是一个函数，它接受 3 个参数，分别是:
* **`remainingRequest`**
* **`previousRequest`**
* **`data`**

**`remainingRequest`**
`remainingRequest` 表示**剩余需要处理的 `loader` 的绝对路径以!分割组成的字符串。**
{% asset_img 7.webp %}

同样我们在上边的 `loader` 中为每个 `normal loader` 分别添加一个 `pitch` 属性，我们以 `loader2.pitch` 来举例:

在 `loader.pitch` 函数中 `remainingRequest` 的值为 `xxx/loader3.js` 的字符串。如果说后续还存在多个 `loader`,那么他们会以 `!` 进行分割。

> 需要注意的是 `remainingRequest` 与剩余 `loader` 有没有 `pitch` 属性没有关系，无论是否存在 `pitch` 属性 `remainingRequest` 都会计算 `pitch` 阶段还未处理剩余的`loader`。

**`previousRequest`**
在理解了 `remainingRequest` 的概念之后，那么 `pitch loader` 的第二个参数就很好理解了。

**它表示 `pitch` 阶段已经迭代过的 `loader` 按照 `!` 分割组成的字符串。**

> 注意同样 `previousRequest` 和有无 `pitch` 属性没有任何关系。同时`remainingRequest` 和 `previousRequest` 都是不包括自身的(也就是我们例子中都不包含 `loader2` 自身的绝对路径)。

作者：19组清风
链接：https://juejin.cn/post/7036379350710616078/
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

**`data`**
现在让我们来看看 `pitch loader` 最后一个参数。这个参数默认是一个空对象 `{}`。

在 `normalLoader` 与 `pitch Loader` 进行交互正是利用了第三个 `data` 参数。

同样我们以上图中的 `loader2` 来举例:
{% asset_img 9.webp %}

* 当我们在 `loader2.pith` 函数中通过给 `data` 对象上的属性赋值时，比如`data.name="19Qingfeng"`。

* 此时在 `loader2` 函数中可以通过 `this.data.name` 获取到自身 `pitch` 方法中传递的 `19Qingfeng`。

#### `loader` 的 `raw` 属性
值得一提的是日常我们在开发一些 `loader` 时，`normal Loader` 的参数我们讲到过它会接受前置 `normal loader` or 对应资源文件(当它为第一个 `loader` 还未经过任何 `loader` 处理时) 的内容。这个内容默认是一个 `string` 类型的字符串。

但是在我们开发一些特殊的 `loader` 时，比如我们需要处理图片资源时，此时对于图片来说将图片变成字符串明显是不合理的。针对于图片的操作**通常我们需要的是读取图片资源的 `Buffer` 类型而非字符串类型。**
此时我们可以通过 `loader.raw` 标记 `normal loader` 的参数是 `Buffer` 还是`String`:
* 当 `loader.raw` 为 `false` 时，此时我们 `normal loader` 的 `source` 获取的是一个 `String` 类型，这也是默认行为。

* 当 `loader.raw` 为 `true` 时，此时这个 `loader` 的 `normal` 函数接受的`source` 参数就是一个 `Buffer` 类型。
```js
function loader2(source) {
    // 此时source是一个Buffer类型 而非模型的string类型
}

loader2.raw = true

module.exports = loader2
```

#### `normal loader & pitch loader` 返回值
上边其实我们已经详细讲过了关于 `Normal Loader` 和 `Pitch Loader` 的返回值。

* `Normal` 阶段，`loader` 函数的返回值会在 `loader chain` 中进行一层一层传递直到最后一个 `loader` 处理后传递将返回值给 `webpack` 进行传递。

* `Pitch` 阶段，任意一个 `loader` 的 `pitch` 函数如果返回了非 `undefined` 的任何值，会发生**熔断**效果同时将 `pitch` 的返回值传递给 `normal` 阶段 `loader` 的函数。

> 需要额外注意的是，在 `normal` 阶段的最后一个 `loader` 一定需要返回一个 `js` 代码(一个 `module` 的代码，比如包含 `module.exports` 语句)。

关于**熔断**效果我相信大家如果认真看到这里一定能够理解它，如果对于熔断还有疑问的小伙伴我强烈建议再去看看我们上边关于熔断的两张图。

## `Loader` 源码分析
在上边我们对于 `loader` 的基础内容和概念进行了详细的讲解。掌握了上边的内容之后我相信在日常业务中对于绝大多数 `loader` 的场景你都可以游刃有余。

可是作为一个合格的前端工程师，任何一款工具的使用如果仅仅停留在应用方便一定是不合格的。

接下来，让我们从源码出发一步一步去掌握 `webpack` 中是如何实现 `loader` 从而更深层次的理解 `loader` 核心内容与 `loader` 的设计哲学吧！

### 写在源码分析之前
`webpack` 中的 `loader` 机制就独立出来成为了一个 `loader-runner.js`，所以相对于 `loader` 处理的逻辑和 `webpack` 没有过多的耦合比较清晰。

首先，源码分析对于大多数人来说都觉得枯燥无趣,这里我会尽量简化步骤手把手带大家实现一款 `loader-runner` 库。

文章中我想给大家强调的是一个源码流程，而非和真实源码一模一样。这样做的好处是简化了很多边界条件的处理可以更加快速、方便的带大家去掌握 `loader` 背后的设计哲学。

但是并不是说我们实现的 `loader-runner` 并不是源码，我们会在源码的基础上进行分析省略它的冗余步骤并且提出对于源码中部分写法我自己的优化点。

### 前期准备
在进入 `loader-runner` 分析之前，我们让我们先来看一看 `webpack` 中是在哪里进入这个模块调用～

**`webpack` 中通过 `compilation` 对象进行模块编译时，会首先进行匹配 `loader` 处理文件得到结果,之后才会输出给 `webpack` 进行编译。**

简单来说就是在每一个模块 `module` 通过 `webpack` 编译前都会首先根据对应文件后缀寻找匹配到对应的 `loader`，先调用 `loader` 处理资源文件从而将处理后的结果交给 `webpack` 进行编译。

{% asset_img 9.webp %}

在 `webpack` 中的 `_doBuild` 函数中调用了 `runLoaders` 方法，而 `runLoaders` 方法正是来自于 `loader-runner` 库。

简单来说 `webpack` 在进行模块编译时会调用 `_doBuild`，在 `doBuild` 方法内部通过调用 `runLoaders` 方法调用 `loader` 处理模块。

### `runLoaders` 参数
在真正进入 `runLoader` 方法前我们先来看一看 `runLoader` 方法传入的四个参数。

这里为了大家更加单纯的理解 `loader`，我们单独将这四个参数拿出来并不会尽量做到和 `webpack` 编译过程解耦。

* `resource`: `resource` 参数表示需要加载的资源路径。

* `loaders`: 表示需要处理的 `loader` 绝对路径拼接成为的字符串，以 `!` 分割。

* `context`: `loader` 上下文对象，`webpack` 会在进入 `loader` 前创建一系列属性挂载在一个 `object` 上，而后传递给 `loader` 内部。

比如我们上边说到的 `this.getOptions()` 方法获得 `loader` 的配置 `options` 参数就是在进入 `runLoader` 函数前 `webpack` 将 `getOptions` 方法定义在了 `loaderContext` 对象中传递给 `context` 参数。

这里你可以理解这个参数就是 `loader` 的上下文对象，简单来说就是我们使用 `loader` 函数中的 `this`，为了让大家更好的理解 `loader-runner`，这里我们并不会涉及 `webpack` 对于 `context` 对象的处理。

* `processResource`: 读取资源文件的方法。

同样源码中的 `processResource` 涉及了很多 `plugin hooks` 相关逻辑，这里你可以简单理解为它是一个 `fs.readFile` 方法就可以。本质上这个参数的核心就是通过 `processResource` 方法按照路径来读取我们的资源文件。

### 构造场景
正所谓磨刀不费砍柴功，在了解了 `webpack` 源码中调用 `loader` 函数时传递了四个参数之后，接下来让我们来模拟一下这四个参数吧。

#### 目录构建

首先让我们先创建一个名为 `loader-runner` 的文件夹,其次在 `loader-runner` 下我们分别创建一个 `loader-runner/loaders` 文件夹和 `loader-runner/index.js` 两个文件分别存放我们的 `loader` 和对应的模拟入口文件:

同时让我们在 `loader-runner` 下创建一个 `title.js`,它存放需要 `loader` 编译的 `js` 文件内容
```js
// title.js
require('inline1-loader!inline2-loader!./title.js');
```

此时我们应该拥有了这样的目录:
{% asset_img 10.webp %}

#### 创建 `Loader`

接下里让我们来给 `loader-runner/loaders` 下补充一些 `loader` 吧，这里我为`loader-runner/loaders` 创建了 `8` 个 `loader`:
{% asset_img 11.webp %}

他们的内容非常简单，而且都是类似的。比如 `inline1-loader` 中:
```js
// 每一个loader文件中都存在对应的 normal loader和 pitch loader
// normal loader中打印一句 文件名: normal 和 对应的接受参数
// pitch loader 中打印一句 文件名 pitch
function loader(source) {
  console.log('inline1: normal', source);
  return source + '//inline1';
}

loader.pitch = function () {
  console.log('inline1 pitch');
};

module.exports = loader;
```

同理 `post1-loader` 中：
```js
function loader(source) {
  console.log('post1: normal', source);
  return source + '//post1';
}

loader.pitch = function () {
  console.log('post2 pitch');
};

module.exports = loader;
```

剩下的文件具体内容都是一样的，请小伙伴们自己动手创建一下～
此时我们就已经创建好了这8个我们需要使用到的 `loader` 了。

#### 创建入口文件
上边我们说到过在 `webpack` 源码中是通过 `compilation` 编译时调用 `_doBuild` 方法时创造参数并且调用 `runLoaders` 方法。

这里我们为了和 `webpack` 构建流程尽量解耦，所以我们先动手自己构建一下 `runLoaders` 函数需要的参数。
```js
// loader-runner/index.js
// 入口文件
const fs = require('fs');
const path = require('path');
const { runLoaders } = require('loader-runner');

// 模块路径
const filePath = path.resolve(__dirname, './title.js');

// 模拟模块内容和.title.js一模一样的内容
const request = 'inline1-loader!inline2-loader!./title.js';

// 模拟webpack配置
const rules = [
  // 普通loader
  {
    test: /\.js$/,
    use: ['normal1-loader', 'normal2-loader'],
  },
  // 前置loader
  {
    test: /\.js$/,
    use: ['pre1-loader', 'pre2-loader'],
    enforce: 'pre',
  },
  // 后置loader
  {
    test: /\.js$/,
    use: ['post1-loader', 'post2-loader'],
  },
];

// 从文件引入路径中提取inline loader 同时将文件路径中的 -!、!!、! 等标志inline-loader的规则删除掉
const parts = request.replace(/^-?!+/, '').split('!');

// 获取文件路径
const sourcePath = parts.pop();

// 获取inlineLoader
const inlineLoaders = parts;

// 处理rules中的loader规则
const preLoaders = [],
  normalLoaders = [],
  postLoaders = [];

rules.forEach((rule) => {
  // 如果匹配情况下
  if (rule.test.test(sourcePath)) {
    switch (rule.enforce) {
      case 'pre':
        preLoaders.push(...rule.use);
        break;
      case 'post':
        postLoaders.push(...rule.use);
        break;
      default:
        normalLoaders.push(...rule.use);
        break;
    }
  }
});

/**
 * 根据inlineLoader的规则过滤需要的loader
 * https://webpack.js.org/concepts/loaders/
 * !: 单个！开头，排除所有normal-loader.
 * !!: 两个!!开头 仅剩余 inline-loader 排除所有(pre,normal,post).
 * -!: -!开头将会禁用所有pre、normal类型的loader，剩余post和normal类型的.
 */
let loaders = [];
if (request.startsWith('!!')) {
  loaders.push(...inlineLoaders);
} else if (request.startsWith('-!')) {
  loaders.push(...postLoaders, ...inlineLoaders);
} else if (request.startsWith('!')) {
  loaders.push(...postLoaders, ...inlineLoaders, ...preLoaders);
} else {
  loaders.push(
    ...[...postLoaders, ...inlineLoaders, ...normalLoaders, ...preLoaders]
  );
}

// 将loader转化为loader所在文件路径
// webpack下默认是针对于配置中的resolveLoader的路径进行解析 这里为了模拟我们省略了webpack中的路径解析
const resolveLoader = (loader) => path.resolve(__dirname, './loaders', loader);

// 获得需要处理的loaders路径
loaders = loaders.map(resolveLoader);

runLoaders(
  {
    resource: filePath, // 加载的模块路径
    loaders, // 需要处理的loader数组
    context: { name: '19Qingfeng' }, // 传递的上下文对象
    readResource: fs.readFile.bind(fs), // 读取文件的方法
    // processResource 参数先忽略
  },
  (error, result) => {
    console.log(error, '存在的错误');
    console.log(result, '结果');
  }
);
```

这里我们通过 `loader-runner/index.js` 来模拟 `webpack` 编译过程中传递给`loader-runner` 中 `runLoader`的参数。

这里有几点需要大家注意:

* `filePath` 是 `title.js` 的模块路径，换而言之我们就是通过 `loader` 来处理这个 `title.js` 文件。

* `request` 是我们模拟 `title.js` 中的内容，它其实和 `title.js` 文件内容是一模一样的，这里我们为了方便模拟 `webpack` 解析 `loader`的 处理规则所以直接将 `title.js` 的文件内容放在了 `request` 字符串中。

* 这里我们给 `runLoaders` 中第一参数对应的属性分别是:

    * `resource` 表示需要 `loader` 编译的模块路径。

    * `loaders` 表示本次 `loader` 处理，需要有哪些 `loader` 进行处理。(它是一个所有需要处理的 `loader` 文件路径组成的数组)

    * `context` 表示 `loader` 的上下文对象，真实源码中 `webpack` 会在进入 `runLoaders` 方法前对这个对象进行额外加工，这里我们不做过多处理，它就是`loader` 中的 `this` 上下文。

    * `readResource` 这个参数表示 `runLoaders` 方法中会以我们传入的这个参数去读取 `resource` 需要加载的文件路径从而得到文件内容。


* `runLoaders` 函数第二个参数传入的是一个 `callback` 表示本次 `loader` 处理完成的结果。

> 这里传入的 `loaders` 参数的顺序是我刻意而为之的。是按照 `pitch` 阶段的执行顺序来处理的: `post` -> `inline` -> `normal` -> `pre`。

> 这里我们在上边的代码中调用了原版的 `loader-runner` 处理，让我们一起先来看看原版的结果吧。

{% asset_img 12.webp %}

我们可以看到原版 `loader-runner` 中 `runLoaders` 我们传入的第二个参数`callback` 在处理完成后会得到两个参数。

* `error`: 如果 `runLoaders` 函数执行过程中遇到错误那么这个参数将会变成错误内容，否则将为 `null`。

* `result`: 如果 `runLoaders` 函数执行完毕并且没有存在任何错误，那么这个`result` 将会存在以下属性:

    * `result`：它是一个数组用来表示本次经过所有 `loaders` **处理完毕后的文件内容。**
    * `resourceBuffer`： 它是一个 `Buffer` 内容，这个内容表示我们的资源文件原始内容转化为 `Buffer` 的结果。
    * 其他参数是关于 `webpack` 构建与缓存时的参数，这里我们可以先不用关系这些参数

#### 流程梳理
在了解到原版的 `runLoaders` 方法接受的参数以及返回的结果之后，接下来让我们来实现自己的 `runLoaders` 方法吧。

首先，让我们先在一步一步先从创建目录开始吧！
{% asset_img 13.webp %}

这里我们在 `loader-runner` 目录下创建了一个 `core/index.js` 作为我们将要实现的 `loader-runner` 模块。

创建好目录之后让我们再来回顾一下这张图吧:
{% asset_img 14.webp %}

这里我想给大家强调的是整个 `runLoaders` 函数的核心逻辑就是**接受待处理的资源文件路径，根据传入的 `Loaders` 首先经过 `pitch` 阶段读取资源文件内容再经过`normal` 阶段处理资源文件内容最终得到返回结果。**

把握这样一个核心执行过程，剩下的一些边界情况的处理。

接下来让我们正式进入 `runLoaders` 方法的实现。

#### 进入源码

##### 创建 `loaders` 对象
```js
// loader-runner
const fs = require('fs')

function runLoaders(options, callback) {
  // 需要处理的资源绝对路径
  const resource = options.resource || ''
  // 需要处理的所有loaders 组成的绝对路径数组
  let loaders = options.loaders || []
  // loader执行上下文对象 每个loader中的this就会指向这个loaderContext
  const context = options.context || {}
  // 读取资源内容的方法
  const readResource = options.readResource || fs.readFile.bind(fs);
  // 根据loaders路径数组创建loaders对象
  loaders = loaders.map(createLoaderObject);
}
```

首先第一步我们在 `runLoaders` 函数中保存了外部传入的 `options` 中的参数，细心的同学可能已经发现了我们通过 `createLoaderObject` 方法对传入的 `loaders` 进行 `map` 处理。

`createLoaderObject` 方法做的事情很简单，其实就是将原本 `loaders` 路径数组中每个路径修改成为了一个对象,让我们来实现一下这个方法吧:
```js
/**
 *
 * 通过loader的绝对路径地址创建loader对象
 * @param {*} loader loader的绝对路径地址
 */
function createLoaderObject(loader) {
  const obj = {
    normal: null, // loader normal 函数本身
    pitch: null, // loader pitch 函数
    raw: null, // 表示normal loader处理文件内容时 是否需要将内容转为buffer对象
    // pitch阶段通过给data赋值 normal阶段通过this.data取值 用来保存传递的data
    data: null,
    pitchExecuted: false, // 标记这个loader的pitch函数时候已经执行过
    normalExecuted: false, // 表示这个loader的normal阶段是否已经执行过
    request: loader, // 保存当前loader资源绝对路径
  };
  // 按照路径加载loader模块 真实源码中通过loadLoader加载还支持ESM模块 咱们这里仅仅支持CJS语法
  const normalLoader = require(obj.request);
  // 赋值
  obj.normal = normalLoader;
  obj.pitch = normalLoader.pitch;
  // 转化时需要buffer/string   raw为true时为buffer false时为string
  obj.raw = normalLoader.raw;
  return obj;
}
```

这里我们通过 `createLoaderObject` 将 `loader` 的绝对路径地址转化成为了`loader` 对象，并且赋值了一些核心的属性 `normal`、`pitch`、`raw`、`data` 等等。

##### 赋值 `loaderContext`，处理上下文对象
在通过 `createLoaderObject` 函数将路径转化为 `loader` 对象后，让我们回到`runLoaders` 函数中，让我们紧跟着来处理 `loader` 中的上下文对象`loaderContext`:
```js
function runLoaders(options, callback) {
  // 需要处理的资源绝对路径
  const resource = options.resource || '';
  // 需要处理的所有loaders 组成的绝对路径数组
  let loaders = options.loaders || [];
  // loader执行上下文对象 每个loader中的this就会指向这个loaderContext
  const loaderContext = options.context || {};
  // 读取资源内容的方法
  const readResource = options.readResource || fs.readFile.bind(fs);
  // 根据loaders路径数组创建loaders对象
  loader = loader.map(createLoaderObject);
  // 处理loaderContext 也就是loader中的this对象
  loaderContext.resourcePath = resource; // 资源路径绝对地址 
  loaderContext.readResource = readResource; // 读取资源文件的方法
  loaderContext.loaderIndex = 0; // 我们通过loaderIndex来执行对应的loader
  loaderContext.loaders = loaders; // 所有的loader对象
  loaderContext.data = null;
  // 标志异步loader的对象属性
  loaderContext.async = null;
  loaderContext.callback = null;
  // request 保存所有loader路径和资源路径
  // 这里我们将它全部转化为inline-loader的形式(字符串拼接的"!"分割的形式)
  // 注意同时在结尾拼接了资源路径哦～
  Object.defineProperty(loaderContext, 'request', {
    enumerable: true,
    get: function () {
      return loaderContext.loaders
        .map((l) => l.request)
        .concat(loaderContext.resourcePath || '')
        .join('!');
    },
  });
  // 保存剩下的请求 不包含自身(以LoaderIndex分界) 包含资源路径
  Object.defineProperty(loaderContext, 'remainingRequest', {
    enumerable: true,
    get: function () {
      return loaderContext.loaders
        .slice(loaderContext + 1)
        .map((i) => i.request)
        .concat(loaderContext.resourcePath)
        .join('!');
    },
  });
  // 保存剩下的请求，包含自身也包含资源路径
  Object.defineProperty(loaderContext, 'currentRequest', {
    enumerable: true,
    get: function () {
      return loaderContext.loaders
        .slice(loaderContext)
        .map((l) => l.request)
        .concat(loaderContext.resourcePath)
        .join('!');
    },
  });
  // 已经处理过的loader请求 不包含自身 不包含资源路径
  Object.defineProperty(loaderContext, 'previousRequest', {
    enumerable: true,
    get: function () {
      return loaderContext.loaders
        .slice(0, loaderContext.index)
        .map((l) => l.request)
        .join('!');
    },
  });
  // 通过代理保存pitch存储的值 pitch方法中的第三个参数可以修改 通过normal中的this.data可以获得对应loader的pitch方法操作的data
  Object.defineProperty(loaderContext, 'data', {
    enumerable: true,
    get: function () {
      return loaderContext.loaders[loaderContext.loaderIndex].data;
    },
  });
}
```

这里我们为 `loaderIndex` 上下文对象上定义了一系列属性，比如其中我们通过`loaderIndex` 控制当前 `loaders` 列表中，当前执行到第几个 `loader` 以及当 前 `data`、`async`、`callback`等等属性。

其实这里我相信大家对这些参数属性是不是有一种似曾相识的感觉，之前我们说到过在`loader` 中我们通过 `this` 上下文对象灵活的对于 `loader` 进行配置，这里我们定义的 `loaderContext` 恰恰正是 `this` 对象。

> 关于上下文对象这些参数的含义，我们在上文的 `loader` 开发阶段已经大概讲过对应API的用法和含义, 如果忘记的同学可以回到上边再次温习温习。

> 这里我们仅仅对于这些属性进行了定义，同时我希望在看到目前定义属性时同学们可以联想到每个API相应的用法。

定义完成 `loaderContext` 属性后，让我们再次回到 `runLoaders` 方法中:
```js
function runLoaders(options, callback) {
  //...


  // 用来存储读取资源文件的二进制内容 (转化前的原始文件内容)
  const processOptions = {
    resourceBuffer: null,
  };
  // 处理完loaders对象和loaderContext上下文对象后
  // 根据流程我们需要开始迭代loaders--从pitch阶段开始迭代
  // 按照 post-inline-normal-pre 顺序迭代pitch
  iteratePitchingLoaders(processOptions, loaderContext, (err, result) => {
    callback(err, {
      result,
      resourceBuffer: processOptions.resourceBuffer,
    });
  });
}
```

让我们稍微回顾一下 `runLoaders` 传入的 `calback` 回调方法中 `result` 的打印值：
{% asset_img 15.webp %}

这里我们定义的 `processOptions` 中的 `resourceBuffer` 正是 `result` 中的`resourceBuffer`： **原始(未经 `loader` 处理)的资源文件内容的 `Buffer` 对象。**

##### `iteratePitchingLoaders`
在创建 `loader` 对象、赋值 `loaderContext` 属性后，按照之前的流程图。我们就要进入每一个 `loader` 的 `pitch` 执行阶段。

上边我们定义了 `iteratePitchingLoaders` 函数，并且为他传入了三个参数:

* `processOptions`: 我们上述定义的对象，它存在一个 `resourceBuffer` 属性用来保存未经过 `loader` 处理前 `Buffer` 类型的资源文件内容。

* `loaderContext`: `loader` 上下文对象。

* `callback`: 这个方法内部调用了 `runLoaders`方法外部传入的 `callback`，用来在回调函数中调用最终的 `runLoaders` 方法的结果。

了解了传入的参数后，让我们一起来看看 `iteratePitchingLoaders` 的实现吧。
```js
/**
 * 迭代pitch-loaders
 * 核心思路: 执行第一个loader的pitch 依次迭代 如果到了最后一个结束 就开始读取文件
 * @param {*} options processOptions对象
 * @param {*} loaderContext loader中的this对象
 * @param {*} callback runLoaders中的callback函数
 */
function iteratePitchingLoaders(options, loaderContext, callback) {
  // 超出loader个数 表示所有pitch已经结束 那么此时需要开始读取资源文件内容
  if (loaderContext.loaderIndex >= loaderContext.loaders.length) {
    return processResource(options, loaderContext, callback);
  }

  const currentLoaderObject = loaderContext.loaders[loaderContext.loaderIndex];

  // 当前loader的pitch已经执行过了 继续递归执行下一个
  if (currentLoaderObject.pitchExecuted) {
    loaderContext.loaderIndex++;
    return iteratePitchingLoaders(options, loaderContext, callback);
  }
  
  const pitchFunction = currentLoaderObject.pitch;

  // 标记当前loader pitch已经执行过
  currentLoaderObject.pitchExecuted = true;

  // 如果当前loader不存在pitch阶段
  if (!currentLoaderObject.pitch) {
    return iteratePitchingLoaders(options, loaderContext, callback);
  }

  // 存在pitch阶段 并且当前pitch loader也未执行过 调用loader的pitch函数
  runSyncOrAsync(
    pitchFunction,
    loaderContext,
    [
      currentLoaderObject.remainingRequest,
      currentLoaderObject.previousRequest,
      currentLoaderObject.data,
    ],
    function (err, ...args) {
      if (err) {
        // 存在错误直接调用callback 表示runLoaders执行完毕
        return callback(err);
      }
      // 根据返回值 判断是否需要熔断 or 继续往下执行下一个pitch
      // pitch函数存在返回值 -> 进行熔断 掉头执行normal-loader
      // pitch函数不存在返回值 -> 继续迭代下一个 iteratePitchLoader
      const hasArg = args.some((i) => i !== undefined);
      if (hasArg) {
        loaderContext.loaderIndex--;
        // 熔断 直接返回调用normal-loader
        iterateNormalLoaders(options, loaderContext, args, callback);
      } else {
        // 这个pitch-loader执行完毕后 继续调用下一个loader
        iteratePitchingLoaders(options, loaderContext, callback);
      }
    }
  );
}
```
我带大家稍微来看看 `iteratePitchingLoaders` 这个方法，它做的事情非常简单，**本质上就是通过 `loaderContext.loaderIndex` 来递归迭代每一个 `loader` 对象的 `pitch` 方法**。

这里有几点需要给大家提示下:

* `processResource` 方法是读取资源文件内容的方法，按照上文流程图中的步骤当所有 `pitch` 执行完毕后我们需要读取资源文件内容了。

* `runSyncOrAsync` 方法是执行调用 `loader` 函数的方法,`loader` 的执行有两种方式同步/异步，这里正是通过这个方法进行的统一处理。

* `iterateNormalLoaders` 方法是迭代 `normal loader` 的方法。

> 上述这三个方法的实现细节这里你可以不用关心，只需要清楚`iteratePitchingLoaders` 的流程就可以。

* 需要额外注意的是，我们在模拟入口文件中传入的 `loader` 顺序是按照 `[...post,...inline,...normal,...pre]` 的传入的。所以内部我们通过`loaderContext.loaderIndex` 从下标 `0` 开始迭代正好符合 `pitch` 阶段。

##### `runSyncOrAsync`
了解了 `iteratePitchingLoaders` 是如何迭代 `pitch loader`，我们一起来看看运行 `loader` 的方法 `runSyncOrAsync`:
```js
/**
 *
 * 执行loader 同步/异步
 * @param {*} fn 需要被执行的函数
 * @param {*} context loader的上下文对象
 * @param {*} args [remainingRequest,previousRequest,currentLoaderObj.data = {}]
 * @param {*} callback 外部传入的callback (runLoaders方法的形参)
 */
function runSyncOrAsync(fn, context, args, callback) {
  // 是否同步 默认同步loader 表示当前loader执行完自动依次迭代执行
  let isSync = true;
  // 表示传入的fn是否已经执行过了 用来标记重复执行
  let isDone = false;

  // 定义 this.callback
  // 同时this.async 通过闭包访问调用innerCallback 表示异步loader执行完毕
  const innerCallback = (context.callback = function () {
    isDone = true;
    // 当调用this.callback时 标记不走loader函数的return了
    isSync = false;
    callback(null, ...arguments);
  });

  // 定义异步 this.async
  // 每次loader调用都会执行runSyncOrAsync都会重新定义一个context.async方法
  context.async = function () {
    isSync = false; // 将本次同步变更成为异步
    return innerCallback;
  };

  // 调用pitch-loader执行 将this传递成为loaderContext 同时传递三个参数
  // 返回pitch函数的返回值 甄别是否进行熔断
  const result = fn.apply(context, args);

  if (isSync) {
    isDone = true;
    if (result === undefined) {
      return callback();
    }
    // 如果 loader返回的是一个Promise 异步loader
    if (
      result &&
      typeof result === 'object' &&
      typeof result.then === 'function'
    ) {
      // 同样等待Promise结束后直接熔断 否则Reject 直接callback错误
      return result.then((r) => callback(null, r), callback);
    }
    // 非Promise 切存在执行结果 进行熔断
    return callback(null, result);
  }
}
```

`runSyncOrAsync` 接受四个参数，分别是

* `fn` 需要被调用的函数
* `context` 被调用的 `fn` 函数内部的 `this` 指针
* `args` 被调用函数的 `fn` 传入的参数
* `callback` 用来表示 `loader(fn)` 执行完毕后调用的回调函数。

它的实现很简单，通过闭包结合 `isSync` 变量实现异步 `this.async/this.callback` 这两个 `loader API` 的实现。

**最终，`loader` 执行完毕 `runSyncOrAsync` 方法会将 `loader` 执行完毕的返回值传递给 `callback` 函数的第二个参数**。

实现了 `runSyncOrAsync`，了解如何执行 `loader` 之后让我们回过头分析`iteratePitchingLoaders` 中的 `runSyncOrAsync`方法。

在 `iteratePitchingLoaders` 函数中我们通过 `runSyncOrAsync` 去执行对应`pitch loader`，分别传入了这四个参数：

* `pitchFunction` 作为需要执行的 `fn`。

* `loaderContext` 表示 `pitch loader` 函数中的 `this` 上下文对象。

* `[currentLoaderObject.remainingRequest,currentLoaderObject.previousRequest,currentLoaderObject.data]`。上文我们说到过 `pitch loader` 函数会接受三个参数分别是剩下的 `loader` 请求, 已经处理过的 `loader` 请求以及作为传递给 `normal` 阶段的 `data`。


* 第四个参数是一个回调函数，**它表示 `pitch loader` 函数执行完毕后这个`callback` 会被调用，如果 `pitch loader` 存在返回值那么它的第二个参数则会接受到 `pitch loader` 执行后的返回值**。

这里我想和大家强调一下 `iteratePitchingLoaders` 中调用 `runSyncOrAsync` 执行 `loader` 时候**传入的第四个 `callback` 函数**：
```js
  runSyncOrAsync(
    pitchFunction,
    loaderContext,
    [
      currentLoaderObject.remainingRequest,
      currentLoaderObject.previousRequest,
      currentLoaderObject.data,
    ],
    function (err, ...args) {
      if (err) {
        // 存在错误直接调用callback 表示runLoaders执行完毕
        return callback(err);
      }
      // 根据返回值 判断是否需要熔断 or 继续往下执行下一个pitch
      // pitch函数存在返回值 -> 进行熔断 掉头执行normal-loader
      // pitch函数不存在返回值 -> 继续迭代下一个 iteratePitchLoader
      const hasArg = args.some((i) => i !== undefined);
      if (hasArg) {
        loaderContext.loaderIndex--;
        // 熔断 直接返回调用normal-loader
        iterateNormalLoaders(options, loaderContext, args, callback);
      } else {
        // 这个pitch-loader执行完毕后 继续调用下一个loader
        iteratePitchingLoaders(options, loaderContext, callback);
      }
    }
  );
```

上边我们提到过第四个参数会在 `pitch loader` 函数执行完毕后 or 报错后`callback` 会被调用。

如果存在错误，那么直接调用 `runLoaders` 传入的 `callback(err)`。

如果不存在错误，这里我们对于**除开第一个表示错误的参数剩余参数做了判断，我们知道这个参数表示 `loader` 执行完毕的返回值，让我们再来回顾一下 `pitch` 阶段的流程图:**
{% asset_img 16.webp %}

**任何一个 `loader` 的 `pitch` 阶段如何返回了非 `undefined` 的任何值，那么此时 `loader` 将会发生熔断的效果：立即掉头执行 `normal loader` 并且将`pitch` 阶段的返回值传递给 `normal loader`**。

所以这里我们通过 `callback` 中判断，如果 `args` 中存在任何一个非 `undefined` 的返回值。那么此时将 `loaderContext.loaderIndex` 递减，从而开始迭代 `normal loader`。

如果 `pitch loader` 运行结束后不存在返回值或者说返回的是 `undefined`，那么此时继续递归调用下一个 `pitch loader`。

> 针对于 `pitch loader` 的大致流程我们在这里就告一段落了，如果同学们还有疑问可以在重新翻看一些或者在评论区来一起讨论。其实在把握 `loader` 执行过程之后，单独代码逻辑来说我相信对于大家理解起来都不是很难，这也就是为什么前边我会花很多篇幅去讲诉 `loader` 的基础用法。


##### `processResource`
结束了 `iteratePitchingLoaders` 之后，迭代完成所有的 `pitch loader` 之后。下一步是什么呢？

如果忘记的同学建议一定要去翻看一下 `loader` 的执行流程图，此时应该是读取资源文件内容了，也就是我们上边没有完成的 `processResource` 方法。
这个方法做的事情非常简单：

* 按照传入的方法读取文件内容，同时将得到的文件 `Buffer` 类型的内容保存进入`processOptions.resourceBuffer` 中去。

* 拿到文件内容后，将文件内容传入 `normal loader` 之后执行`iterateNormalLoaders` 迭代执行 `normal loader`。
```js
/**
 *
 * 读取文件方法
 * @param {*} options
 * @param {*} loaderContext
 * @param {*} callback
 */
function processResource(options, loaderContext, callback) {
  // 重置越界的 loaderContext.loaderIndex
  // 达到倒叙执行 pre -> normal -> inline -> post
  loaderContext.loaderIndex = loaderContext.loaders.length - 1;
  const resource = loaderContext.resourcePath;
  // 读取文件内容
  loaderContext.readResource(resource, (err, buffer) => {
    if (err) {
      return callback(err);
    }
    // 保存原始文件内容的buffer 相当于processOptions.resourceBuffer = buffer
    options.resourceBuffer = buffer;
    // 同时将读取到的文件内容传入iterateNormalLoaders 进行迭代`normal loader`
    iterateNormalLoaders(options, loaderContext, [buffer], callback);
  });
}
```

它的代码其实比较容易理解，这里需要注意的有三点：

* `loaderIndex` 在迭代 `pitch loader` 中越界了(也就是等于 `loaderContext.loaders.length`)时才会进入 `processResource` 方法所以此时我们将 `loaderContext.loaderIndex` 重置为 `loaderContext.loaders.lenth -1`。

* `iterateNormalLoaders` 额外传入了一个表示资源文件内容 `[buffer]` 的数组，这是刻意而为之，这里我先买个关子，后续你会发现我为什么这么做。

* 还记得我们在 `loaderContext.loaders` 中保存的 `loaders` 顺序吗，它是按照 `post -> inline -> normal -> pre` 的顺序保存的的，所以此时只要我们按照`loaderIndex` **逆序去迭代，就可以得到 `normal loader` 的顺序**。

##### `iterateNormalLoaders`
完成了 `processResource` 读取文件内容之后，在 `processResource` 以及`iteratePitchingLoaders` 方法中我们都用到的 `iterateNormalLoaders` -- 迭代 `normal loader` 的函数还未实现。

接下来让我们先来看看这个函数的实现吧：
```js
/**
 * 迭代normal-loaders 根据loaderIndex的值进行迭代
 * 核心思路: 迭代完成pitch-loader之后 读取文件 迭代执行normal-loader
 *          或者在pitch-loader中存在返回值 熔断执行normal-loader
 * @param {*} options processOptions对象
 * @param {*} loaderContext loader中的this对象
 * @param {*} args [buffer/any]
 * 当pitch阶段不存在返回值时 此时为即将处理的资源文件
 * 当pitch阶段存在返回值时 此时为pitch阶段的返回值
 * @param {*} callback runLoaders中的callback函数
 */
function iterateNormalLoaders(options, loaderContext, args, callback) {
  // 越界元素判断 越界表示所有normal loader处理完毕 直接调用callback返回
  if (loaderContext.loaderIndex < 0) {
    return callback(null, args);
  }
  const currentLoader = loaderContext.loaders[loaderContext.loaderIndex];
  if (currentLoader.normalExecuted) {
    loaderContext.loaderIndex--;
    return iterateNormalLoaders(options, loaderContext, args, callback);
  }

  const normalFunction = currentLoader.normal;
  // 标记为执行过
  currentLoader.normalExecuted = true;
  // 检查是否执行过
  if (!normalFunction) {
    return iterateNormalLoaders(options, loaderContext, args, callback);
  }
  // 根据loader中raw的值 格式化source
  convertArgs(args, currentLoader.raw);
  // 执行loader
  runSyncOrAsync(normalFunction, loaderContext, args, (err, ...args) => {
    if (err) {
      return callback(err);
    }
    // 继续迭代 注意这里的args是处理过后的args
    iterateNormalLoaders(options, loaderContext, args, callback);
  });
}
```

其实仔细阅读 `iterateNormalLoaders` 的代码它和 `iteratePitchingLoaders` 存在异曲同工之妙，它们的核心都是**基于`loaderContext.loaderIndex` 下标进行迭代 `loaders` 对象分别运行对应的`pitch` 或者 `normal` 函数**。

只不过不同的是 `iteratePitchingLoaders` 仅仅接受三个参数，`iterateNormalLoaders` 额外接受一个 `args` 参数表示资源文件对象的`[Buffer]`（或者发生熔断时 `pitch loader` 的返回值）。这是由于 `pitch loader` 被调用时的参数可以通过 `loaderContext` 来获取( `remainingRequest` 属性等)，而 `normal loader` 的参数需要一层一层将处理后的内容传递下去。

> 同时为什么我们在上边将文件内容处理成数组 `[Buffer]`，正是因为传递给`runSyncOrAsync` 方法时第三个参数是一个数组(表示调用 `loader` 函数时传递给`loader` 函数的参数)，因为通过 `apply` 进行调用所以统一处理为数组会让代码更加方便简洁。

细心的同学会发现在 `iterateNormalLoaders` 中有一个 `convertArgs` 函数的调用，我们先来看一看这个函数的内容：
```js
/**
 *
 * 转化资源source的格式
 * @param {*} args [资源]
 * @param {*} raw Boolean 是否需要Buffer
 * raw为true 表示需要一个Buffer
 * raw为false表示不需要Buffer
 */
function convertArgs(args, raw) {
  if (!raw && Buffer.isBuffer(args[0])) {
    // 我不需要buffer
    args[0] = args[0].toString();
  } else if (raw && typeof args[0] === 'string') {
    // 需要Buffer 资源文件是string类型 转化称为Buffer
    args[0] = Buffer.from(args[0], 'utf8');
  }
}
```

`convertArgs` 方法根据 **`loader` 的 `raw` 属性判断这个 `loader` 在 `normal` 阶段需要接受资源文件内容是 `Buffer` 还是 `String`**。

上边我们讲到过每个 `loader` 都存在一个 `raw` 属性，通过 `loader.raw` 标记`normal loader` 的参数是 `Buffer` 还是 `String`。这个方法正是在 `normal loader` 执行前对于参数进行转化处理的。


#### 大功告成
此时针对于 `loader-runner` 中 `runLoaders` 方法的核心逻辑我们已经全部实现了。

##### 验证执行结果
让我们现在 `loader-runner/core/index.js` 中导出这个方法，同时在模拟入口文件 `loader-runner/index.js` 中替换为我们自己的模块:
```js
// loader-runner/core/index.js

module.exports = {
    runLoaders
}

// loader-runner/index.js
// 入口文件
const fs = require('fs');
const path = require('path');
const { runLoaders } = require('./core/index');



runLoaders(
  {
    resource: filePath, // 加载的模块路径
    loaders, // 需要处理的loader数组
    context: { name: '19Qingfeng' }, // 传递的上下文对象
    readResource: fs.readFile.bind(fs), // 读取文件的方法
    // processResource 参数先忽略
  },
  (error, result) => {
    console.log(error, '存在的错误');
    console.log(result, '结果');
  }
);
```

接来下让我们执行我们的 `loader-runner/index.js`:
{% asset_img 17.webp %}

我们打印出来的 `callback` 中的 `result` 形参对象中的:

* `result` 属性的值是一个数组，它的第一个元素是经过我们所有 `loader` 处理后的文件内容。

* `resourceBuffer` 属性的值是一个 `Buffer`，它的内容是原始未经转化后的资源文件内容，有兴趣的朋友可以自己 `toString()` 一下看看。

至此我们正常阶段的执行结果和原始的 `runLoaders` 方法基本已经一模一样了。

##### 熔断效果验证
此时，让我们再来验证一下熔断效果。

首先让我们修改 `loader-runner/loaders/inline2-loader`，我们在`inline2-loader` 的 `pitch` 函数中返回一个字符串：
```js
function loader(source) {
  console.log('inline2: normal', source);
  return source + '//inline2';
}

loader.pitch = function () {
  console.log('inline2 pitch');
  return '19Qingfeng';
};

module.exports = loader;
```

再次执行我们的 `loader-runner/core/index.js`:
{% asset_img 18.webp %}

正如我们期望的那样～一切正常！

至此，`runLoaders` 的核心源码我已经带大家全部实现了。

> 文章中的完整代码地址[你可以在这里看到](https://github.com/19Qingfeng/19webpack/tree/master/loader-runner)。

## 企业级 `Loader` 应用
在精进了源码部分的知识体系之后，让我们来聊一些比较轻松的话题吧。

接下来让我们手把手来实现一款企业级 `loader` 应用，带领大家真正精通并掌握开源工具中 `loader` 开发者的思想。

### `babel-loader` 流程
老样子～首先让我们来梳理一下关于需要开发 `babel-loader` 的流程吧。
{% asset_img 19.webp %}

让我们一起先来根据这张图来梳理一下。

**所谓 `babel-loader` 实现的功能特别简单，本质上就是通过 `babel-loader` 函数以及对应配置 `loader` 时的参数将我们的 `js` 文件进行转化**， 简单来说这就是babel-loader需要实现的功能。

当然在经过 `babel-loader` 处理后的内容后续还需要交给 `webapck` 进行编译。

### `babel-loader` 实现
```js
const core = require('@babel/core');

/**
 *
 * @param {*} source 源代码内容
 */
function babelLoader(source) {
  // 获取loader参数
  const options = this.getOptions() || {};
  // 通过transform方法进行转化
  const { code, map, ast } = core.transform(source, options);
  // 调用this.callback表示loader执行完毕
  // 同时传递多个参数给下一个loader
  this.callback(null, code, map, ast);
}

module.exports = babelLoader;
```

看起来很简单吧，这里有一些注意点需要和大家阐述下：

* 关于 `babel`的 `api` 如果有不太清楚的同学可以查看我之前的文章，[「前端基建」带你在Babel的世界中畅游](https://juejin.cn/post/7025237833543581732)。

这里我们通过 `core.transform` 将源 `js` 代码进行 `ast` 转化同时通过外部传递的 `options` 选项处理 `ast` 节点的转化，从而按照外部传入规则将 `js` 代码转化为转化后的代码。

* 这里我们通过 `this.getOptions` 方法获得外部 `loader` 传递的参数。

在 `webpack5` 中获取 `loader` 的方法在调用 `runLoaders` 方法时 `webpack` 已经在 `loaderContext` 中添加了这个 `getOptions` 的实现，从而调用 `runLoaders` 方法时传入了处理好的 `loaderContext`参数。

在 `webpack5` 之前并不存在 `this.getOptions` 方法，需要额外通过`loader-utils` 这个包实现获取外部 `loader` 配置参数。

这个方法的实现非常简单，在源码中的 `webpack/lib/NormalModule.js` 中，有兴趣的朋友可以自行翻看。
接下来就让我们稍微来验证一下吧。

### 验证 `babel-loader`
首先让我们重新搭建一个开发目录 `webpack-babel` 文件夹:
{% asset_img 20.webp %}

* `loaders/babel-loaders` 目录下存放我们自己定义的 `babel-loader`。

* `src/index.js` 存放入口文件。

* `webpack.config.js` 为 `webpack` 配置文件。

```js
// src/index.js
// 这里我们使用ES6的语法
const arrowFunction = () => {
  console.log('hello');
};

console.log(arrowFunction);
```

```js
// webpack.config.js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js',
  },
  devtool: 'eval-source-map',
  resolveLoader: {
    modules: [path.resolve(__dirname, './loaders')],
  },
  module: {
    rules: [
      {
        test: /\.js/,
        loader: 'babel-loader',
        options: {
          presets: ['@babel/preset-env'],
        },
      },
    ],
  },
  plugins: [new HtmlWebpackPlugin()],
};
```

```json
// package.json
{
    ...
    // 这里我们定义两个脚本 一个为开发环境下的dev
    // 一个为build打包命令
  "scripts": {
    "dev": "webpack serve --mode developmen",
    "build": "webpack --config ./webpack.config.js"
  },
}
```

不要忘记安装我们需要的包。
```js
yarn add webpack webpack-cli @babel/core @babel/preset-env html-webpack-plugin webpack-dev-server
```

接下来让我们来一起运行 `yarn build` 来看一看打包后输出的 `js` 内容吧：
{% asset_img 21.webp %}

可以看到在 `yarn build` 之后，生产环境打包后的代码原本的箭头函数已经转化成为了普通函数。也就是说我们自定义的 `babel-loader` 功能已经生效了。

### `sourcemap` 完善 `babel-loader`
生产环境下的确是没有任何问题了，可是开发环境呢。这里我们尝试在 `src/index.js` 下 `debugger` 看一下:
```js
// src/index.js
const arrowFunction = () => {
  console.log('hello');
};

debugger;
console.log(arrowFunction);
```

此时我们执行 `yarn dev`，打开生成的 `html` 页面进入 `debugger`：
{% asset_img 22.webp %}

**在 `debugger` 中的代码此时并不是我们真正的源码，是已经被 `babel` 转译后的代码**，这对于日常开发来说无疑是一种灾难，这里我们的 `src/index.js` 的文件内容很简单只有一个箭头函数。可是当项目中代码越来越复杂，这种情况无疑对于我们进行 `debugger` 代码时是一种噩梦。

其实导致这个问题的原因很简单，在 `babel-loader` 编译阶段我们并没有携带任何`sourceMap` 映射。而在 `webpack` 编译阶段即使开启了 `sourceMap` 映射此时也仅仅**只能将 `webpack` 编译后的代码在 `debugger` 中映射到 `webpack` 处理前，也就是已经经历过 `babel-loader` 处理了。**
{% asset_img 23.webp %}

此时，我们需要做的仅仅是需要在 `babel-loader` 转化过程中添加对应的`sourcemap` 返回交给 `webpack` 编译阶段时候携带 `babel-loader` 生成的`sourcemap` 就可以达到我们期望的效果。

让我们来动手实现一下:
```js
const core = require('@babel/core');

/**
 *
 * @param {*} source 源代码内容
 */
function babelLoader(source, sourceMap, meta) {
  // 获取loader参数
  const options = this.getOptions() || {};
  // 生成babel转译阶段的sourcemap
  options.sourceMaps = true;
  // 保存之前loader传递进入的sourceMap
  options.inputSourceMap = sourceMap;
  // 获得处理的资源文件名 babel生成sourcemap时候需要配置filename
  options.filename = this.request.split('!').pop().split('/').pop();
  // 通过transform方法进行转化
  const { code, map, ast } = core.transform(source, options);
  console.log(map, 'map');
  // 调用this.callback表示loader执行完毕
  // 同时传递多个参数给下一个loader
  // 将transform API生成的sourceMap 返回给下一个loader(或者webpack编译阶段)进行处理
  this.callback(null, code, map, ast);
}

module.exports = babelLoader;
```

这里我们在 `babel` 的 `tranform` 方法上接受到了上一次 `loader` 传递过来的`soureMap`结果（如果有的话）。

同时调用 `options.sourceMaps` 告诉 `babel` 在转译时生成 `sourceMap`。

最终将生成的 `source` 在 `this.callback` 中返回。

此时我们就在我们的 `babel` 转译阶段也生成了 `sourcemap` 同时最终会经过`loader-chain` 将生成的 `sourcemap` 传递给 `webpack`。

> 同时额外注意 `webpack` 中 `devtool` 的配置，如果关闭了 `sourceMap` 的话就看不到任何源代码信息～

接下来让我们再次运行 `yarn build` 打开浏览器看一下:
{% asset_img 24.webp %}

大功告成！至此我们的 `babel-loader` 开发就告一断落了。这里我希望通过这个小例子可以带领大家真正进入 `loader` 开发者的世界。


回到我们最开始的内容，`loader` 本质上就是一个函数而已。只不过是我们通过`loader chain` 将多个 `loader` 链接在一起按照一定顺序和规则去执行而已。

我相信阅读到这里的你，在了解了 `loader` 的基础和原理部分这样的小例子对于大家每个人来说都是信手拈来，其实真实开源 `loader` 中相比这里无非也就是多了一些参数校验和边界处理而已。

> `babel-loader` 的实现代码[你可以在这里看到](https://github.com/19Qingfeng/19webpack/tree/master/loader-runner/loaders/babel-loader)。


## 写在结尾
感谢每一位看到这里的小伙伴，关于 `webpack loader` 的分享文章到这里就要结束了。

希望大家以此为起点，在探索 `loader` 的路上越走越远！

其实源码并不是那么晦涩难懂，我相信 `runLoaders` 源码中的设计理念一定会对大家有所帮助，这也是为什么我会花很大篇幅去在源码分析的章节中的原因。我希望带给大家的不仅仅是关于如何实现 `webpack loader`，更多的是我希望带领大家去掌握一种如何阅读源码的方式。

其实认真看到这里的小伙伴会发现 `runLoaders` 方法中有部分内容的确写的也是不过如此，还有很多优化的点嘛哈哈～

大家如果关于 `loader` 有任何疑问也可以在评论区留下你的问题，我们可以一起探讨～
