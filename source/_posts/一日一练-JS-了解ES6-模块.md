---
title: 一日一练-JS 了解ES6 模块
date: 2018-04-25 14:57:13
comments: true
tags:
  - 技术
  - 一日一练
  - JS
categories: 技术
---

> 子曰：最终的杀器-ES6 模块

## 概述
ES6 模块的设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。

ES6 模块不是对象，而是通过`export` 命令显式指定输出的代码，再通过`import` 命令输入。
```js
// ES6 模块
import { stat, exists, readFile } from 'fs'
```
上面代码的实质是从`fs` 模块加载3 个方法，其他方法不加载。这种加载称为“编译时加载” 或者静态加载，即ES6 可用于在编译时就完成模块加载，效率要比CommonJS 模块的加载方式高。

由于ES6 模块是编译时加载，使得静态分析成为可能。

需要注意`this` 的限制。ES6 模块之后，顶层的`this` 指向`undefined`，即不应该在顶层代码使用`this`。

<!--more-->

## export 命令
模块功能主要由两个命令构成：`export` 和`import`。`export` 命令用于规定模块的对外接口，`import` 命令用于输入其他模块提供的功能。

一个模块就是一个独立的文件。使用`export` 命令输出对外接口。
```js
// profile.js
export var firstName = 'Michael'
export var lastName = 'Jackson'
export var year = 1958
```
ES6 将`profile.js` 视为一个模块，`export` 命令对外输出了3 个变量。

`export` 的另一种写法。
```js
// profile.js
var firstName = 'Michael'
var lastName = 'Jackson'
var year = 1958
export { firstName, lastName, year }
```

接口名与模块内部变量之间，必须建立一一对应的关系
```js
// 报错
function f() {}
export f

// 正确
export function f() {}

// 正确
function f() {}
export {f}
```

另外，`export` 语句输出的接口，与其对应的值是动态绑定关系，即通过该接口，可以取到内部实时的值。
```js
export var foo = 'bar'
setTimeout(() => foo = 'bar', 500)
```
上面代码输出变量`foo`，值为`bar`, 500 毫米之后变成`baz`。
> 子曰：前面说了，import 就相当于将代码复制了过来，所以`setTimeout` 的代码仍然可以影响foo 的值

## import 命令
使用`export` 命令定义了模块的对外接口以后，其他JS 文件就可以通过`import` 命令加载这个模块。
```js
// main.js
import {firstName, lastName, year} from './profile.js'

function setName(element) {
  element.textContent = firstName + ' ' + lastName
}
```
还可以使用`as` 关键字，将输入的变量重命名。
```js
import { lastName as surname } from './profile.js'
```
注意：`import` 命令输入的变量都是只读的，因为它的本质是输入接口。也就是说，不允许在加载模块的脚本里，改写接口。
```js
import {a} from './xxx.js'

a = {} // Syntax Error: 'a' is read-only
```

由于`import` 是静态执行，所以不能使用表达式和变量，这些只能在运行时才能得到结果的语法结构。
```js
// 报错
import { 'f' + 'oo'} from 'my_module'

// 报错
let module = 'my_module'
import { foo } from module

// 报错
if (x = 1) {
  import { foo } from 'module1'
} else {
  import { foo } from 'module2'
}
```

## 模块的整体加载
除了指定加载某个输出值，还可以使用整体加载，即用星号（`*`）指定一个对象，所有输出值都加载在这个对象上面。

```js
// circle.js
export function area (radius) {
  return Math.PI * radius * radius
}

export function circumference (radius) {
  return 2 * Math.PI * radius
}
```
现在加载这个模块。
```js
// math.js
import { area, circumference } from './circle'

console.log('圆面积：' + area(4))
console.log('圆周长：' + circumference(14))
```
整体加载方法如下。
```js
import * as circle from './circle'

console.log('圆面积：' + circle.area(4))
console.log('圆周长：' + circle.circumference(14))
```

## export default 命令
使用`export default` 命令，为模块指定默认输出。
```js
// export-default.js
export default function () {
  console.log('foo')
}
```
其他模块加载该模块时，`import` 命令可以为该匿名函数指定任意名字。
```js
// import-default.js
import customName from './export-default'
customName() // => 'foo'
```
需要注意的是，这时`import` 命令后面，不使用大括号。

正因为`export default` 明in个其实是输入一个叫做`default` 的变量，所以它后面不能跟变量声明语句。
```js
// 正确
export var a = 1

// 正确
var a = 1
export default a

// 错误
export default var a = 1
```

因为`export default` 命令的本质是将后民安的值，赋给`default` 变量，所以可以直接将一个值写在`export default` 之后。
```js
// 正确
export default 42

// 报错
export 42
```

`export default` 也可以用来输出类。
```js
// MyClass.js
export default class { }

// main.js
import MyClass from 'MyClass'
let o = new MyClass()
```

## export 与import 的复合写法
如果在一个模块中，先输入后输出一个模块，`import` 语句可以与`export` 语句写在一起。
```js
export { foo, bar } from 'my_module'

// 可以简单理解为
import { foo, bar } from 'my_module'
export { foo, bar }
```
需要注意的是，写成一行以后，`foo` 和`bar` 实际上并没有被导入当前模块，只是相当于对外转发了这两个接口，导致当前模块不能直接使用`foo` 和`bar`。

## 跨模块常量
`const` 声明的常量只在当前代码块有效。如果想设置跨模块的常量（即跨多个文件），或者说一个值要被多个模块共享，可以采用下面的写法。
```js
// constants.js 模块
export const A = 1
export const B = 2

// test1.js 模块
import * as constants from './constants'
console.log(constants.A) // => 1

// test2.js 模块
import {A, B} from './constants'
console.log(B) // => 2
```

如果要使用的变量非常多，可以建立一个专门的`constants` 目录，将各种变量写在不同的文件里面。
```js
// constants/db.js
export const db = {
  url: 'http://my.couchdbserver.local:5984',
  admin_username: 'admin',
  admin_password: 'admin password'
}

// constants/user.js
export const users = ['root', 'admin', 'staff', 'ceo', 'chief', 'moderator']
```
然后，将这些文件输出的常量，合并在`index.js` 里面。
```js
// constants/index.js
export {db} from './db'
export {users} from './users'
```
使用的时候，直接加载`index.js` 就可以了
```js
// script.js
import {db, users} from './index'
```

## import()
### 简介
`import` 命令会被JavaScript 引擎静态分析，先于模块内的其他语句执行（`import` 命令叫做“连接binding” 其实更合适）。
```js
// 报错
if (x === 2) {
  import MyModule from './myModule'
}
```
上面代码中，引擎处理`import` 是在编译时，这是不会去分析或执行`if` 语句，所以`import` 语句放在`if` 语句块之中毫无意义，因此会报语法错误，而不是执行错误。也就是说，`import` 和`export` 命令只能在模块的顶层，不能在代码块之中。

因此，有一个[提案](https://github.com/tc39/proposal-dynamic-import)，建议引入`import()` 函数，完成动态加载。
```js
import(specifier)
```
上面代码中，`import` 函数的参数`specifier`，指定所要加载的模块的位置。`import` 命令能够接受什么参数，`import()` 函数就能接受什么参数，两者的主要区别在于后者是动态加载。

`import()` 返回一个Promise 对象。
```js
const main = document.querySelector('main')

import(`./section-modules/${someVariable}.js`)
  .then(module => {
    module.loadPageInto(main)
  })
  .catch(err => {
    main.textContent = err.message
  })
```
1. `import()` 函数可以用在任何地方，不仅仅是模块，非模块的脚本也可以使用。它是运行时执行，也就是说，什么时候运行到这一句，就会加载指定的模块。
2. 另外，`import()` 函数与所加载的模块没有静态连接关系，这点也是与`import` 命令不同。
3. `import()` 类似于Node 的`require` 方法，区别主要是前者是异步加载，后者是同步加载。

### 注意点
`import()` 加载模块成功以后，这个模块会作为一个对象，当作`then` 方法的参数。因此，可以使用对象解构赋值的语法，获取输出接口。
```js
import('./myModule.js')
  .then(({export1, export2}) => {
    //...
  })
```
如果想同时加载多个模块，可以采用下面的写法。
```js
Promise.all([
  import('./module1.js'),
  import('./module2.js')
])
.then(([module1, module2]) => {
  ...
})
```
`import()` 也可以用在`async` 函数之中。
```js
async function main() {
  const myModule = await import('./myModule.js')
  const {export1, export2} = await import('./myModule.js')
  const [module1, module2, module3] =
    await Promise.all([
      import('./module1.js'),
      import('./module2.js'),
      import('./module3.js')
    ])
}
main()
```


## 参考文档
1. [es6 模块语法 阮一峰](http://es6.ruanyifeng.com/#docs/module)
1. [es6 模块的加载实现 阮一峰](http://es6.ruanyifeng.com/#docs/module-loader)
