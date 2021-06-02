---
title: 一日一练-JS 扁平化数数组
date: 2021-06-02 13:37:01
tags:
  - 技术
  - 一日一练
  - flat
categories: 技术
---

## 问
如何进行扁平化数字

## 答
有如下几种方案

<!--more-->

### 一、使用flat

`flat()` 方法会按照一个可指定的深度递归遍历数组，并将所有元素与遍历到的子数组中的元素合并为一个新数组返回。
```js
var arr1 = [1, 2, [3, 4]]
arr1.flat()
// [1, 2, 3, 4]

var arr2 = [1, 2, [3, 4, [5, 6]]]
arr2.flat()
// [1, 2, 3, 4, [5, 6]]

var arr3 = [1, 2, [3, 4, [5, 6]]]
arr3.flat(2)
// [1, 2, 3, 4, 5, 6]

// 使用 Infinity，可展开任意深度的嵌套数组
var arr4 = [1, 2, [3, 4, [5, 6, [7, 8, [9, 10]]]]]
arr4.flat(Infinity)
// [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

// flat() 方法会移除数组中的空项
var arr4 = [1, 2, , 4, 5]
arr4.flat()
// [1, 2, 4, 5]
```

### 二、使用 reduce 和 concat
```js
var arr = [1, 2, [3, 4]];

// 展开一层数组
arr.flat()
// 等效于
arr.reduce((acc, val) => acc.concat(val), [])
// [1, 2, 3, 4]

// 使用扩展运算符 ...
const flattened = arr => [].concat(...arr)
```

### 三、reduce + concat + isArray + recursivity
```js
// 使用 reduce、concat 和递归展开无限多层嵌套的数组
var arr1 = [1,2,3,[1,2,3,4, [2,3,4]]]

function flatDeep(arr, d = 1) {
    return d > 0 ? arr.reduce((acc, val) => acc.concat(Array.isArray(val) ? flatDeep(val, d - 1) : val), [])
                 : arr.slice()
}

flatDeep(arr1, Infinity)
// [1, 2, 3, 1, 2, 3, 4, 2, 3, 4]
```

### 四、forEach + isArray + push + recursivity
```js
// forEach 遍历数组会自动跳过空元素
const eachFlat = (arr = [], depth = 1) => {
    const result = []; // 缓存递归结果
    // 开始递归
    (function flat(arr, depth) {
        // forEach 会自动去除数组空位
        arr.forEach((item) => {
            // 控制递归深度
            if (Array.isArray(item) && depth > 0) {
            // 递归数组
            flat(item, depth - 1)
            } else {
            // 缓存元素
            result.push(item)
            }
        })
    })(arr, depth)
    // 返回递归结果
    return result
}

// for of 循环不能去除数组空位，需要手动去除
const  forFlat = (arr = [], depth = 1) => {
	const result = [];
	(function flat(arr, depth) {
		for (let item of arr) {
			if (Array.isArray(item) && depth > 0) {
				flat(item, depth - 1 )
			} else {
				// 去除空元素， 添加非 undefined 元素
				item !== void 0 && result.push(item)
			}
		}
	})(arr, depth)
	return result
}
```

### 五、使用堆栈 stack
无递归
```js
// 无递归/数组扁平化，使用堆栈
// 注意：深度的控制比较低效，因为需要检查每一个值的深度
// 也可能在shift / unshift 上进行 w/o 反转，但是末端的数组 OPs 更快
function flatten(input) {
	const stack = [...input]
	const res = []
	while(stack.length) {
		// 使用 pop 从 stack 中取出并移除值
		const next = stack.pop()
		if (Array.isArray(next)){
			// 使用 push 送回内层数组中的元素，不会改动原始输入
			stack.push(...next)
		} else {
			res.push(next)
		}
	}
	// 反转恢复原数组的顺序
	return res.reverse()
}
```

有递归
```js
// 递归版本的反嵌套
function flatten(array){
    var flattend = [];
    (function flat(array) {
        array.forEach(function(el) {
        if (Array.isArray(el)) flat(el)
        else flattend.push(el)
        })
    })(array)
    return flattend
}
```

### 六、使用 generator 函数
```js
function * flatten(array) {
    for (const item of array) {
        if (Array.isArray(item)) {
            yield * flatten(item)
        } else {
            yield item
        }
    }
}
var arr = [1, 2, [3, 4, [5, 6]]]
var flattened = [...flatten(arr)]
// [1, 2, 3, 4, 5, 6]
```

## 原文
1.[MDN Array.prototype.flat()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/flat)