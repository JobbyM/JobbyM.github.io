---
title: 了解 Vue 中混入 mixin 的使用
date: 2021-02-22 17:27:54
tags:
  - 技术
  - Vue
  - mixin
categories: 技术
---

> 子曰：万里之行，始于足下。

## 环境
1. OS: windows 10
2. 应用：Vuejs + Android 壳的 hybrid app 应用
3. IDE： Visual Studio Code

<!--more-->

## 问题
在最近的 hybrid app 项目中，有一个需求是需要使用 aliyun 的 oss 进行对 pdf、txt、word、xlsx 的预览，而图片、视频则需要在调用 Native 的方法进行预览。

这个需求在项目的多处使用，例如列表页、详情页等。

## 解决方案
开始简单的将预览的相关代码复制一份，COPY TO Everywhere。后期在测试、维护的时候发现，当一处有BUG 时，需要将所有的修改都 COPY 一份，导致工作量大增，而且极易出错。

## 优化方案
后来发现可以使用 Vue 的混入 mixin 来解决代码在多处复用的问题。

## 混入 mixin

### 基础
混入 (mixin) 提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能。一个混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被“混合”进入该组件本身的选项。

### 选项合并
当组件和混入对象含有同名选项时，这些选项将以恰当的方式进行“合并”。
1. 数据对象在内部会进行递归合并，并在发生冲突时移组件数据有限（`组件优先`）。
2. 值为对象的选项，例如 `methods`、`components` 和 `directives`，将被合并为同一个对象。两个对象键名冲突时，取组件对象的键值对（`组件优先`）。
3. 同名钩子函数（例如：`beforeCreate` 、`created` 、`beforeMount` 、`mounted` 、`beforeUpdate` 、`updated` 、`activated` 、`deactivated` 、`beforeDestroy` 、`destroyed` ）将合并为一个数组，因此都将被调用。另外，混入对象的钩子将在组件自身钩子`之前`调用（`混入优先`）。

## 例子
1. 在文件中新建一个 mixin 文件夹，文件夹中在建一个 mixin.js 用来放入混入的代码。
```
src
-- api
-- common
-- mixin
---- mixin.js
-- components
```

2. 在 mixin.js 写入需要混入的代码
```js
export default {
  data () {
    return {
      name: 'mixin-name',
      title: 'mixin-title'
    }
  },

  created(){
    console.log('mixin-created')
  },
  mounted(){
    console.log('mixin-created')
  },

  methods: {
    handleClick(){
      console.log('mixin-methods-handleClick')
      this.name = 'mixin-name-2'
    },
    handleClick2(){
      console.log('mixin-methods-handleClick2')
    }
  },
}
```
3. 在 .vue 单文件中引入
```html
<template>
<div class="layout">
  <h1>{{name}}</h1>
  <h2>{{title}}</h2>
  <button @click="handleClick">btn1</button>
  <button @click="handleClick2">btn2</button>
</div>
</template>
<script>
import mixin from "@/views/index/mixin/mixin.js";
export default {
  mixins: [mixin],
  data () {
    return {
      title: 'title'
    }
  },
  
  created(){
    console.log('sfc-created')
  },
  mounted(){
    console.log('sfc-created')
  },

  methods: {
    handleClick2(){
      console.log('sfc-methods-handleClick2')
    }
  },
}
</script>
<style scoped>
</style>
```

4. 页面运行效果
图片0
{% asset_img 0.png %}
图片1
{% asset_img 1.png %}

5. 点击 btn1 的效果
图片2
{% asset_img 2.png %}
图片3
{% asset_img 3.png %}

6. 点击 btn2 的效果
图片4
{% asset_img 4.png %}
图片5
{% asset_img 5.png %}

## 验证
1.图片0 中的title ，验证了
```
1. 数据对象在内部会进行递归合并，并在发生冲突时移组件数据有限（`组件优先`）。
```

2.图片1 中的`mixin-created` 在`sfc-created` 验证了
```
3. 同名钩子函数（例如：`beforeCreate` 、`created` 、`beforeMount` 、`mounted` 、`beforeUpdate` 、`updated` 、`activated` 、`deactivated` 、`beforeDestroy` 、`destroyed` ）将合并为一个数组，因此都将被调用。另外，混入对象的钩子将在组件自身钩子`之前`调用（`混入优先`）。
```

3.点击 btn2 ，使得图片5 中的输出的是 `sfc-methods-handleClick2` 验证了
```
2. 值为对象的选项，例如 `methods`、`components` 和 `directives`，将被合并为同一个对象。两个对象键名冲突时，取组件对象的键值对（`组件优先`）。
```

## 参考文档
1.[Vue.js 官方文档混入](https://cn.vuejs.org/v2/guide/mixins.html)

