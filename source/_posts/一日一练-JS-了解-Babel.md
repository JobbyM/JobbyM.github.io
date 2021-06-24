---
title: 一日一练-JS 了解 Babel
date: 2021-06-23 15:19:35
tags:
  - 技术
  - Babel
  - 转载
categories: 技术
---

> 子曰：万里之行，始于足下。

本文是根据知乎上的这篇文章 [一口（很长的）气了解 babel](https://zhuanlan.zhihu.com/p/43249121) 整理而来的。

说到 babel，一连串名词会蹦出来：

* babel-cli
* babel-core
* babel-runtime
* babel-node
* babel-polyfill
* ...

这些都是 babel 吗？他们分别是做什么的？有区别吗？

<!--more-->

## babel 到底做了什么？怎么做的？
简单来说把 JavaScript 中 es2015/2016 的新语法转化为 es5，让低端运行环境(如浏览器和 node )能够认识并执行。本文以 babel 6.x 为基准进行讨论。最近 babel 出了 7.x，放在最后聊。

严格来说，babel 也可以转化为更低的规范。但以目前情况来说，es5 规范已经足以覆盖绝大部分浏览器，因此常规来说转到 es5 是一个安全且流行的做法。

## 使用方法
总共存在三种方式：

* 使用单体文件 (standalone script)
* 命令行 (cli)
* 构建工具的插件 (webpack 的 babel-loader, rollup 的 rollup-plugin-babel)。

其中后面两种比较常见。第二种多见于 package.json 中的 scripts 段落中的某条命令；第三种就直接集成到构建工具中。

这三种方式只有入口不同而已，调用的 babel 内核，处理方式都是一样的，所以我们先不纠结入口的问题。

## 运行方式和插件
babel 总共分为三个阶段：解析，转换，生成。

babel 本身不具有任何转化功能，它把转化的功能都分解到一个个 plugin 里面。因此当我们不配置任何插件时，经过 babel 的代码和输入是相同的。

插件总共分为两种：

* 当我们添加 **语法插件** 之后，在解析这一步就使得 babel 能够解析更多的语法。(顺带一提，babel 内部使用的解析类库叫做 babylon，并非 babel 自行开发)
举个简单的例子，当我们定义或者调用方法时，最后一个参数之后是不允许增加逗号的，如 `callFoo(param1, param2,)` 就是非法的。如果源码是这种写法，经过 babel 之后就会提示语法错误。

但最近的 JS 提案中已经允许了这种新的写法(让代码 diff 更加清晰)。为了避免 babel 报错，就需要增加语法插件 `babel-plugin-syntax-trailing-function-commas`

* 当我们添加 **转译插件** 之后，在转换这一步把源码转换并输出。这也是我们使用 babel 最本质的需求。
比起语法插件，转译插件其实更好理解，比如箭头函数 `(a) => a` 就会转化为 `function (a) {return a}`。完成这个工作的插件叫做 `babel-plugin-transform-es2015-arrow-functions`。

同一类语法可能同时存在语法插件版本和转译插件版本。**如果我们使用了转译插件，就不用再使用语法插件了。**

## 配置文件
既然插件是 babel 的根本，那如何使用呢？总共分为 2 个步骤：

1. 将插件的名字增加到配置文件中 (根目录下创建 .babelrc 或者 package.json 的 `babel` 里面，格式相同)
2. 使用 `npm install babel-plugin-xxx` 进行安装

具体书写格式就不详述了。

## preset
比如 es2015 是一套规范，包含大概十几二十个转译插件。如果每次要开发者一个个添加并安装，配置文件很长不说，npm install 的时间也会很长，更不谈我们可能还要同时使用其他规范呢。

为了解决这个问题，babel 还提供了一组插件的集合。因为常用，所以不必重复定义 & 安装。(单点和套餐的差别，套餐省下了巨多的时间和配置的精力)

preset 分为以下几种：

* 官方内容，目前包括 env, react, flow, minify 等。这里最重要的是 env，后面会详细介绍。
* stage-x，这里面包含的都是当年最新规范的草案，每年更新。
这里面还细分为

    * Stage 0 - 稻草人: 只是一个想法，经过 TC39 成员提出即可。
    * Stage 1 - 提案: 初步尝试。
    * Stage 2 - 初稿: 完成初步规范。
    * Stage 3 - 候选: 完成规范和浏览器初步实现。
    * Stage 4 - 完成: 将被添加到下一年度发布。
例如 syntax-dynamic-import 就是 stage-2 的内容，transform-object-rest-spread 就是 stage-3 的内容。
此外，低一级的 stage 会包含所有高级 stage 的内容，例如 stage-1 会包含 stage-2, stage-3 的所有内容。
stage-4 在下一年更新会直接放到 env 中，所以没有单独的 stage-4 可供使用。

* es201x, latest
这些是已经纳入到标准规范的语法。例如 es2015 包含 arrow-functions，es2017 包含 syntax-trailing-function-commas。但因为 env 的出现，使得 es2016 和 es2017 都已经废弃。所以我们经常可以看到 es2015 被单独列出来，但极少看到其他两个。
latest 是 env 的雏形，它是一个每年更新的 preset，目的是包含所有 es201x。但也是因为更加灵活的 env 的出现，已经废弃。

## 执行顺序
很简单的几条原则：

* Plugin 会运行在 Preset 之前。
* Plugin 会从前到后顺序执行。
* Preset 的顺序则 **刚好相反**(从后向前)。

preset 的逆向顺序主要是为了保证向后兼容，因为大多数用户的编写顺序是 ['es2015', 'stage-0']。这样必须先执行 stage-0 才能确保 babel 不报错。因此我们编排 preset 的时候，也要注意顺序，**其实只要按照规范的时间顺序列出即可。**

## 插件和 preset 的配置项
简略情况下，插件和 preset 只要列出字符串格式的名字即可。但如果某个 preset 或者插件需要一些配置项(或者说参数)，就需要把自己先变成数组。第一个元素依然是字符串，表示自己的名字；第二个元素是一个对象，即配置对象。

最需要配置的当属 env，如下：
```json
"presets": [
    // 带了配置项，自己变成数组
    [
        // 第一个元素依然是名字
        "env",
        // 第二个元素是对象，列出配置项
        {
          "module": false
        }
    ],

    // 不带配置项，直接列出名字
    "stage-2"
]
```

## env (重点)
因为 env 最为常用也最重要，所以我们有必要重点关注。

env 的核心目的是通过配置得知目标环境的特点，然后只做必要的转换。例如目标浏览器支持 es2015，那么 es2015 这个 preset 其实是不需要的，于是代码就可以小一点(一般转化后的代码总是更长)，构建时间也可以缩短一些。

如果不写任何配置项，env 等价于 latest，也等价于 es2015 + es2016 + es2017 三个相加(不包含 stage-x 中的插件)。env 包含的插件列表维护在这里

下面列出几种比较常用的配置方法：
```json
{
  "presets": [
    ["env", {
      "targets": {
        "browsers": ["last 2 versions", "safari >= 7"]
      }
    }]
  ]
}
```

如上配置将考虑所有浏览器的最新2个版本(safari大于等于7.0的版本)的特性，将必要的代码进行转换。而这些版本已有的功能就不进行转化了。这里的语法可以参考 [browserslist](https://github.com/browserslist/browserslists)
```json
{
  "presets": [
    ["env", {
      "targets": {
        "node": "6.10"
      }
    }]
  ]
}
```

如上配置将目标设置为 nodejs，并且支持 6.10 及以上的版本。也可以使用 `node: 'current'` 来支持最新稳定版本。例如箭头函数在 nodejs 6 及以上将不被转化，但如果是 nodejs 0.12 就会被转化了。

另外一个有用的配置项是 `modules`。它的取值可以是 `amd`, `umd`, `systemjs`, `commonjs` 和 `false`。这可以让 babel 以特定的模块化格式来输出代码。如果选择 `false` 就不进行模块化处理。


## 其他配套工具
以上讨论了 babel 的核心处理机制和配置方法等，不论任何入口调用 babel 都走这一套。但文章开头提的那一堆 `babel-*` 还是让人一头雾水。实际上这些 `babel-*` 大多是不同的入口(方式)来使用 babel，下面来简单介绍一下。

### babel-cli
顾名思义，cli 就是命令行工具。安装了 `babel-cli` 就能够在命令行中使用 `babel` 命令来编译文件。

在开发 npm package 时经常会使用如下模式：

* 把 `babel-cli` 安装为 `devDependencies`
* 在 package.json 中添加 `scripts` (比如 `prepublish`)，使用 `babel` 命令编译文件
* `npm publish`

这样既可以使用较新规范的 JS 语法编写源码，同时又能支持旧版环境。因为项目可能不太大，用不到构建工具 (webpack 或者 rollup)，于是在发布之前用 `babel-cli` 进行处理。

### babel-node
`babel-node` 是 `babel-cli` 的一部分，它不需要单独安装。

它的作用是在 node 环境中，直接运行 es2015 的代码，而不需要额外进行转码。例如我们有一个 js 文件以 es2015 的语法进行编写(如使用了箭头函数)。我们可以直接使用 `babel-node es2015.js` 进行执行，而不用再进行转码了。

可以说：`babel-node` = `babel-polyfill` + `babel-register`。那这两位又是谁呢？

### babel-register
babel-register 模块改写 `require` 命令，为它加上一个钩子。此后，每当使用 `require` 加载 `.js`、`.jsx`、`.es` 和 `.es6` 后缀名的文件，就会先用 babel 进行转码。

使用时，必须首先加载 `require('babel-register')`。

需要注意的是，babel-register 只会对 `require` 命令加载的文件转码，而 **不会对当前文件转码。**

另外，由于它是实时转码，所以 **只适合在开发环境使用。**

### babel-polyfill
babel 默认只转换 js 语法，而不转换新的 API，比如 Iterator、Generator、Set、Maps、Proxy、Reflect、Symbol、Promise 等全局对象，以及一些定义在全局对象上的方法(比如 `Object.assign`)都不会转码。

举例来说，es2015 在 Array 对象上新增了 `Array.from` 方法。babel 就不会转码这个方法。如果想让这个方法运行，必须使用 `babel-polyfill`。(内部集成了 `core-js` 和 `regenerator`)

使用时，在所有代码运行之前增加 `require('babel-polyfill')`。或者更常规的操作是在 `webpack.config.js` 中将 `babel-polyfill` 作为第一个 entry。因此必须把 `babel-polyfill` 作为 `dependencies` 而不是 `devDependencies`

`babel-polyfill` 主要有两个缺点：

1. 使用 `babel-polyfill` 会导致打出来的包非常大，因为 `babel-polyfill` 是一个整体，把所有方法都加到原型链上。比如我们只使用了 `Array.from`，但它把 `Object.defineProperty` 也给加上了，这就是一种浪费了。这个问题可以通过单独使用 `core-js` 的某个类库来解决，`core-js` 都是分开的。
2. `babel-polyfill` 会污染全局变量，给很多类的原型链上都作了修改，如果我们开发的也是一个类库供其他开发者使用，这种情况就会变得非常不可控。

因此在实际使用中，如果我们无法忍受这两个缺点(尤其是第二个)，通常我们会倾向于使用 `babel-plugin-transform-runtime`。

但如果代码中包含高版本 js 中类型的实例方法 (例如 `[1,2,3].includes(1)`)，这还是要使用 polyfill。

### babel-runtime 和 babel-plugin-transform-runtime (重点)
我们时常在项目中看到 .babelrc 中使用 `babel-plugin-transform-runtime`，而 `package.json` 中的 `dependencies` (注意不是 `devDependencies`) 又包含了 `babel-runtime`，那这两个是不是成套使用的呢？他们又起什么作用呢？

先说 `babel-plugin-transform-runtime`。

babel 会转换 js 语法，之前已经提过了。以 `async/await` 举例，如果不使用这个 plugin (即默认情况)，转换后的代码大概是：
```js
// babel 添加一个方法，把 async 转化为 generator
function _asyncToGenerator(fn) { return function () {....}} // 很长很长一段

// 具体使用处
var _ref = _asyncToGenerator(function* (arg1, arg2) {
  yield (0, something)(arg1, arg2);
});
```

不用过于纠结具体的语法，只需看到，这个 `_asyncToGenerator` 在当前文件被定义，然后被使用了，以替换源代码的 `await`。但每个被转化的文件都会插入一段 `_asyncToGenerator` 这就导致重复和浪费了。

在使用了 `babel-plugin-transform-runtime` 了之后，转化后的代码会变成
```js
// 从直接定义改为引用，这样就不会重复定义了。
var _asyncToGenerator2 = require('babel-runtime/helpers/asyncToGenerator');
var _asyncToGenerator3 = _interopRequireDefault(_asyncToGenerator2);

// 具体使用处是一样的
var _ref = _asyncToGenerator3(function* (arg1, arg2) {
  yield (0, something)(arg1, arg2);
});
```

从定义方法改成引用，那重复定义就变成了重复引用，就不存在代码重复的问题了。

但在这里，我们也发现 `babel-runtime` 出场了，它就是这些方法的集合处，也因此，**在使用 `babel-plugin-transform-runtime` 的时候必须把 `babel-runtime` 当做依赖。**

再说 `babel-runtime`，它内部集成了

1. `core-js`: 转换一些内置类 (`Promise`, `Symbols` 等等) 和静态方法 (`Array.from `等)。绝大部分转换是这里做的。自动引入。
2. `regenerator`: 作为 `core-js` 的拾遗补漏，主要是 `generator/yield` 和 `async/await` 两组的支持。当代码中有使用 `generators/async` 时自动引入。
3. helpers, 如上面的 `asyncToGenerator` 就是其中之一，其他还有如 `jsx`, `classCallCheck` 等等，可以查看 [babel-helpers](https://github.com/babel/babel/blob/6.x/packages/babel-helpers/src/helpers.js)。在代码中有内置的 helpers 使用时(如上面的第一段代码)移除定义，并插入引用(于是就变成了第二段代码)。

`babel-plugin-transform-runtime` **不支持** 实例方法 (例如 `[1,2,3].includes(1)`)

此外补充一点，把 helpers 抽离并统一起来，避免重复代码的工作还有一个 plugin 也能做，叫做 `babel-plugin-external-helpers`。但因为我们使用的 `transform-runtime` 已经包含了这个功能，因此不必重复使用。而且 babel 的作者们也已经开始讨论这两个插件过于类似，正在讨论在 babel 7 中把 external-helpers 删除，讨论在 [issue#5699](https://github.com/babel/babel/issues/5699) 中。

### babel-loader
前面提过 babel 的三种使用方法，并且已经介绍过了 `babel-cli`。但一些大型的项目都会有构建工具 (如 webpack 或 rollup) 来进行代码构建和压缩 (uglify)。理论上来说，我们也可以对压缩后的代码进行 babel 处理，但那会非常慢。因此如果在 uglify 之前就加入 babel 处理，岂不完美？

所以就有了 babel 插入到构建工具内部这样的需求。以 webpack 为例，webpack 有 loader 的概念，因此就出现了 `babel-loader`。

和 `babel-cli` 一样，`babel-loader` 也会读取 .babelrc 或者 package.json 中的 `babel` 段作为自己的配置，之后的内核处理也是相同。唯一比 `babel-cli` 复杂的是，它需要和 webpack 交互，因此需要在 webpack 这边进行配置。比较常见的如下：
```js
module: {
  rules: [
    {
      test: /\.js$/,
      exclude: /(node_modules|bower_components)/,
      loader: 'babel-loader'
    }
  ]
}
```

如果想在这里传入 babel 的配置项，也可以把改成：
```js
// loader: 'babel-loader' 改成如下：
use: {
  loader: 'babel-loader',
  options: {
    // 配置项在这里
  }
}
```

这里的配置项优先级是最高的。但我认为放到单独的配置文件中更加清晰合理，可读性强一些。

### 小结一下
{% asset_img 1.jpg %}

## Babel 7.x
最近 babel 发布了 7.0。因为上面部分都是针对 6.x 编写的，所以我们关注一下 7.0 带来的变化(核心机制方面没有变化，插件，preset，解析转译生成这些都没有变化)

我只挑选一些和开发者关系比较大的列在这里，省略的多数是针对某一个 plugin 的改动。完整的列表可以参考[官网](https://babeljs.io/docs/en/v7-migration)。

## preset 的变更：淘汰 es201x，删除 stage-x，强推 env (重点)
淘汰 es201x 的目的是把选择环境的工作交给 env 自动进行，而不需要开发者投入精力。**凡是使用 es201x 的开发者，都应当使用 env 进行替换**。但这里的淘汰 (原文 deprecated) 并不是删除，只是不推荐使用了，不好说 babel 8 就真的删了。

与之相比，stage-x 就没那么好运了，它们直接被删了。这是因为 babel 团队认为为这些 “不稳定的草案” 花费精力去更新 preset 相当浪费。stage-x 虽然删除了，但它包含的插件并没有删除(只是被更名了，可以看下面一节)，我们依然可以显式地声明这些插件来获得等价的效果。[完整列表](https://github.com/babel/babel/tree/master/packages/babel-preset-stage-0#babelpreset-stage-0)

为了减少开发者替换配置文件的机械工作，babel 开发了一款 `babel-upgrade` 的[工具](https://github.com/babel/babel-upgrade)，它会检测 babel 配置中的 stage-x 并且替换成对应的 plugins。除此之外它还有其他功能，我们一会儿再详细看。(总之目的就是让你更加平滑地迁移到 babel 7)

## npm package 名称的变化 (重点)
这是 babel 7 的一个重大变化，把所有 `babel-*` 重命名为 `@babel/*`，例如：

1. `babel-cli` 变成了 `@babel/cli`。
2. `babel-preset-env` 变成了 `@babel/preset-env`。进一步，还可以省略 `preset` 而简写为 `@babel/env`。
3. `babel-plugin-transform-arrow-functions` 变成了 `@babel/plugin-transform-arrow-functions`。和 `preset` 一样，`plugin` 也可以省略，于是简写为 `@babel/transform-arrow-functions`。


这个变化不单单应用于 package.json 的依赖中，包括 .babelrc 的配置 (`plugins`, `presets`) 也要这么写，为了保持一致。例如
```diff
{
  "presets": [
-   "env"
+   "@babel/preset-env"
  ]
}
```

顺带提一句，上面提过的 babel 解析语法的内核 `babylon` 现在重命名为 `@babel/parser`，看起来是被收编了。

上文提过的 stage-x 被删除了，它包含的插件虽然保留，但也被重命名了。babel 团队希望更明显地区分已经位于规范中的插件 (如 es2015 的 `babel-plugin-transform-arrow-functions`) 和仅仅位于草案中的插件 (如 stage-0 的 `@babel/plugin-proposal-function-bind`)。方式就是在名字中增加 `proposal`，所有包含在 stage-x 的转译插件都使用了这个前缀，语法插件不在其列。

最后，如果插件名称中包含了规范名称 (`-es2015-`, `-es3-` 之类的)，一律删除。例如 `babel-plugin-transform-es2015-classes` 变成了 `@babel/plugin-transform-classes`。(这个插件我自己没有单独用过，惭愧)

## 不再支持低版本 node
babel 7.0 开始不再支持 nodejs 0.10, 0.12, 4, 5 这四个版本，相当于要求 nodejs >= 6 (当前 nodejs LTS 是 8，要求也不算太过分吧)。

这里的不再支持，指的是在这些低版本 node 环境中不能使用 babel 转译代码，但 babel 转译后的代码依然能在这些环境上运行，这点不要混淆。

## only 和 ignore 匹配规则的变化
在 babel 6 时，`ignore` 选项如果包含 `*.foo.js`，实际上的含义 (转化为 glob) 是 `./**/*.foo.js`，也就是当前目录 **包括子目录** 的所有 `foo.js` 结尾的文件。这可能和开发者常规的认识有悖。

于是在 babel 7，相同的表达式 `*.foo.js` 只作用于当前目录，不作用于子目录。如果依然想作用于子目录的，就要按照 glob 的完整规范书写为 `./**/*.foo.js` 才可以。`only` 也是相同。

这个规则变化只作用于通配符，不作用于路径。所以 `node_modules` 依然包含所有它的子目录，而不单单只有一层。(否则全世界开发者都要爆炸)

## @babel/node 从 @babel/cli 中独立了
和 babel 6 不同，如果要使用 `@babel/node`，就必须单独安装，并添加到依赖中。

## babel-upgrade
在提到删除 stage-x 时候提过这个工具，它的目的是帮助用户自动化地从 babel 6 升级到 7。

这款升级工具的功能包括：(这里并不列出完整列表，只列出比较重要和常用的内容)

1. package.json
2. 把依赖(和开发依赖)中所有的 `babel-*` 替换为 `@babel/*`
3. 把这些 `@babel/*` 依赖的版本更新为最新版 (例如 ^7.0.0)
4. 如果 `scripts` 中有使用 `babel-node`，自动添加 `@babel/node` 为开发依赖
5. 如果有 `babel` 配置项，检查其中的 `plugins` 和 `presets`，把短名 (`env`) 替换为完整的名字 (`@babel/preset-env`)
6. .babelrc
7. 检查其中的 `plugins` 和 `presets`，把短名 (`env`) 替换为完整的名字 (`@babel/preset-env`)
8. 检查是否包含 `preset-stage-x`，如有替换为对应的插件并添加到 `plugins`
使用方式如下：
```bash
# 不安装到本地而是直接运行命令，npm 的新功能
npx babel-upgrade --write

# 或者常规方式
npm i babel-upgrade -g
babel-upgrade --write
```

`babel-upgrade` 工具本身也还在开发中，还列出了许多 TODO 没有完成，因此之后的功能可能会更加丰富，例如上面提过的 `ignore` 的通配符转化等等。