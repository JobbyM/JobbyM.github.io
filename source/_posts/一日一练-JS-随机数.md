---
title: 一日一练-JS 随机数
date: 2021-07-19 16:14:01
tags:
  - 技术
  - 一日一练
  - 随机数
  - Math.random
categories: 技术
---


## Math.random()
`random()` 方法可返回介于 0（包含） ~ 1（不包含） 之间的一个随机数。
```js
Math.random()
```

## 用例
### 获取 1 到 100 之间的随机数
```js
Math.floor((Math.random() * 100) + 1)
```

<!--more-->

### 返回 min（包含）～ max（不包含）之间的数字：
```js
function getRndInteger(min, max) {
  return Math.floor(Math.random() * (max - min) ) + min;
}
```

### 以下函数返回 min（包含）～ max（包含）之间的数字：
```js
function getRndInteger(min, max) {
  return Math.floor(Math.random() * (max - min + 1) ) + min;
}
```