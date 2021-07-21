---
title: 一日一练-JS 数组乱序
date: 2021-07-19 16:30:01
tags:
  - 技术
  - 一日一练
  - 数组
  - 洗牌算法
  - shuffle
  - 转载
categories: 技术
---


本文转载自[JS: 数组乱序](https://www.cnblogs.com/guolao/p/10173537.html)，有部分删节，看原文请到原地址。

## 数字乱序
顾名思义，数组乱序就是把数组存储的值的顺序都打乱。

## Fisher-Yates shuffle
著名的洗牌算法，原理就是遍历数组元素，将当前元素与随机抽取的一个剩余元素进行交换。

<!--more-->

下列表格遍历元素是从后往前：
{% asset_img 1.png %}

代码如下：
```js
function shuffle (arr) {
  for (let i = arr.length - 1; i >= 0; i --) {
    let rIndex = Math.floor(Math.random() * (i + 1)) 
    // 打印交换值
    // console.log(i, rIndex)
    let temp = arr[rIndex]
    arr[rIndex] = arr[i]
    arr[i] = temp
  }
  return arr
}

let arr = [1,2,3,4,5,6]
shuffle(arr)
console.log(arr) // [1, 3, 5, 4, 2, 6]
```

现在测试一下是否真的实现了乱序：
```js
// 使用 res 存储结果
let res = {}
let times = 100000
for (let i = 0; i < times; i ++) {
  // 使用 [1, 2, 3] 进行简答测试
  let key = JSON.stringify(shuffle([1,2,3]))
  res[key] ? res[key] ++ : res[key] = 1
}
for (let key in res) {
  res[key] =  Number.parseFloat(res[key]/times * 100).toFixed(3) + '%'
}
```
{% asset_img 2.png %}

### 固定一个值
在乱序的同时，固定一个下标的值，使其位置不变，方法有很多，这里只给出一种：
```js
function shuffle(arr, index) {
  let res = []
  // 取出固定值
  let fix = arr.splice(index, 1)[0]
  for (let i = arr.length - 1; i >= 0; i --) {
    let rIndex = Math.floor(Math.random()*(i + 1))
    res.push(arr[rIndex])
    arr.splice(rIndex, 1)
  }
  // 将固定值放入指定位置
  res.splice(index, 0, fix)
  return res
}
// 多次多次运行，可以看出数组下标为 1 的值始终是固定的
shuffle([1, 2, 3, 4, 5, 6, 7], 1)
```

{% asset_img 3.png %}

这里同样测试一下是否实现了真正的乱序：
```js
// 使用 res 存储结果
let res = {}
let times = 100000
for (let i = 0; i < times; i ++) {
  // 使用 [1, 2, 3] 进行简答测试
  let key = JSON.stringify(shuffle([1,2,3], 1))
  res[key] ? res[key] ++ : res[key] = 1
}
for (let key in res) {
  res[key] =  Number.parseFloat(res[key]/times * 100).toFixed(3) + '%'
}

// 固定的同时，依然是乱序的
res
```

{% asset_img 4.png %}