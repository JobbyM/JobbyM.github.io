---
title: 记一次基于vant 组件的二次开发
date: 2020-07-30 16:22:12
tags:
---

## 简介
在开发手机端的H5 项目时，选择了vue + vant生态，在使用[PullRefresh 下拉刷新](https://youzan.github.io/vant/#/zh-CN/pull-refresh) 组件时，需要进行自定义显示。但是首页Tab 下有3 个页面都需要进行定制，为了方便将其封装为一个组件。


## 源码
下面是pullRefresh.vue 以及parent.vue 的相关源码

<!--more-->

### 1.子组件pullRefresh.vue
```vuejs
<template>
  <van-pull-refresh v-model="isLoading" :head-height="80" @refresh="onRefresh">
    <!-- 下拉提示，通过 scale 实现一个缩放效果 -->
    <template #pulling="props">
      <img
        class="doge"
        src="https://img.yzcdn.cn/vant/doge.png"
        :style="{ transform: `scale(${props.distance / 80})` }"
      />
    </template>

    <!-- 释放提示 -->
    <template #loosing>
      <img class="doge" src="https://img.yzcdn.cn/vant/doge.png" />
    </template>

    <!-- 加载提示 -->
    <template #loading>
      <img class="doge" src="https://img.yzcdn.cn/vant/doge-fire.jpg" />
    </template>
    <slot></slot>
  </van-pull-refresh>
</template>
<script>
export default {
  name: 'pull-refresh',
  props: {
    value: {
      type: Boolean,
      default: false,
    }
  },
  data () {
    return {
      isLoading: false,
    }
  },
  watch: {
    value(val) {
      // 3.1 侦听到value 值改为true, 这时的val 为 true
      // 7. 侦听到父组件isLoading 值的改变，这是的val 为false
      if (val == false) {
        // 8. 由于val == false，此时子组件的isLoading 赋值为 false，完成下拉刷新过程
        this.isLoading = false
      }
    }
  },
  methods: {
    // 1. 子组件首次触发onRefresh 事件
    onRefresh () {
      // 2. 更新父组件的value 的值，这里的value 在父组件中是isLoading
      this.$emit('input', true)
      // 4. 触发父组件的refresh 事件，也就是父组件中的onRefresh 事件
      this.$emit('refresh')
    },
  }
}
</script>
<style>
  .doge {
    width: 140px;
    height: 72px;
    margin-top: 8px;
    border-radius: 4px;
  }
</style>
```

### 2.父组件parent.vue
```vuejs
<tempalte>
  <pull-refresh v-model="isLoading" @refresh="onRefresh">
  父组件信息
  </pull-refresh>
</template>
<script>
import pullRefresh from './pullRefresh.vue'
export default {
  components: {
    'pull-refresh': pullRefresh
  },
  data () {
    return {
      // 3.2 由于子组件中产生了input 事件，导致isLoading = true
      isLoading: false,
    }
  },
  methods: {
    // 5. 子组件触发了父组件中的onRefresh 方法
    onRefresh () {
      setTimeout(()=>{

        // 6. 下拉刷新完成时，更新父组件的isLoading 为false，此时触发了子组件中的value 的值改变
        this.isLoading: false
      }, 2000)
    }
  }
}
</script>
```

## 源码解析
使用到的相关知识点：
1. [插槽](https://cn.vuejs.org/v2/guide/components-slots.html)
2. [计算属性和侦听属性](https://cn.vuejs.org/v2/guide/computed.html)
3. [自定义事件](https://cn.vuejs.org/v2/guide/components-custom-events.html)
4. [自定义组件的v-model](https://cn.vuejs.org/v2/guide/components-custom-events.html#%E8%87%AA%E5%AE%9A%E4%B9%89%E7%BB%84%E4%BB%B6%E7%9A%84-v-model)

## 过程分析
在页面上模拟一次下拉刷新
1. 子组件首次触发onRefresh 事件
2. 更新父组件的value 的值，这里的value 在父组件中是isLoading
3. 此时会触发两次改变
  3.1 侦听到value 值改为true, 这时的val 为 true
  3.2 由于子组件中产生了input 事件，导致isLoading = true
4. 触发父组件的refresh 事件，也就是父组件中的onRefresh 事件
5. 子组件触发了父组件中的onRefresh 方法
6. 下拉刷新完成时，更新父组件的isLoading 为false，此时触发了子组件中的value 的值改变
7. 侦听到父组件isLoading 值的改变，这是的val 为false
8. 由于val == false，此时子组件的isLoading 赋值为 false，完成下拉刷新过程

## 总结
项目中还是需要多看多写多总结，这样才能更好的理解Vue