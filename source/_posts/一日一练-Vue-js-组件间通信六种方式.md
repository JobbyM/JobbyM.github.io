---
title: 一日一练-Vue.js 组件间通信六种方式
date: 2021-07-15 00:06:11
tags:
  - 技术
  - 一日一练
  - Vue.js
  - 转载
categories: 技术
---


本文转载自[Vue 组件间通信六种方式（完整版）](https://mp.weixin.qq.com/s?__biz=MzAxODE2MjM1MA==&mid=2651556445&idx=1&sn=dee936663d7125cf8528b1315a5a93ba&scene=21#wechat_redirect)，有部分删节，看原文请到原地址。

本文总结了vue 组件间通信的几种方式，如 props、 $emit / $on、vuex、 $parent / $children、 $attrs / $listeners 和 provide / inject

<!--more-->

## 方法一、props / $emit

### 1.父组件向子组件传值

### 2.子组件向父组件传值（通过事件形式）

### 总结
子组件通过 events 给父组件发送消息，实际上就是子组件把自己的数据发送到父组件。

## 方法二、$emit / $on
**这种方法通过一个空的 Vue 实例作为中央事件总线（事件中心），用它来触发事件和监听事件,巧妙而轻量地实现了任何组件间的通信，包括父子、兄弟、跨级**。当我们的项目比较大时，可以选择更好的状态管理解决方案 vuex。

## 方法三、vuex

## 方法四、 $attrs　/ $listeners
多级组件嵌套需要传递数据时，通常使用的方法是通过 vuex。但如果仅仅是传递数据，而不做中间处理，使用 vuex 处理，未免有点大材小用。为此Vue2.4 版本提供了另一种方法 -- `$attrs` / `$listeners`

* `$attrs`：包含了父作用域中不被 prop 所识别 (且获取) 的特性绑定 (class 和 style 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 `v-bind="$attrs"` 传入内部组件。通常配合 interitAttrs 选项一起使用。

* `$listeners`：包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。它可以通过 `v-on="$listeners"` 传入内部组件

## 方法五、provide / inject

## 方法六、 $parent / $children　＆ ref

## 总结
常见使用场景可以分为三类：

* 父子通信： 父向子传递数据是通过 props，子向父是通过 events（ $emit）；通过父链 / 子链也可以通信（ $parent / $children）；ref 也可以访问组件实例；provide / inject API； $attrs/$listeners

* 兄弟通信： Bus；Vuex

* 跨级通信： Bus；Vuex；provide / inject API、 $attrs/$listeners
