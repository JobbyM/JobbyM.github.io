---
title: React Native 基础入门
date: 2016-10-28 09:54:50
tags:
  - React Native
  - node.js
comments: false
updated:
categories: 技术
---

# 简介
React Native 入门知识点

<!--more-->

## 知识点

1. <Text> 组件专门用来显示文本
2. AppRegistry 模块则是用来告知React Native 哪一个组件被注册为整个应用的根容器，在一般的整个应用中AppRegistry.registerComponent 这个方法只会调用一次。
3. <View> 常用来作其他组件的容器，来帮助控制布局和样式。
4. StyleSheet.create 来集中定义组件的样式。
5. flexDirection 的默认值是column 而不是row，alignItem 的默认值是stretch 而不是flex-start，以及flex 只能指定一个数字值。
6. 在组件的style 中指定flexDirection 可以决定布局的主轴。子元素应该沿着水平轴（row）方向排列，还是沿着竖直轴（column）方法排列呢？默认是竖直轴（column）方向。
7. 在组件的style 中指定justifyContent 可以决定其子元素沿着主轴的排列方式。子元素是应该靠近主轴的起始端还是末尾端分布呢？亦或是应该均匀分布？对应的这些可选项有：flex-start、center、flex-end、space-around 以及space-between。
8. 在组件的style 中指定alignItem 可以决定子元素沿着次轴（与主轴垂直的轴，比如若主轴方向为row，则次轴方向为column）的排列方式。子元素是应该靠近次轴的起始端还是末尾端分布呢？亦或应该均匀分布？对应的这些可选项有：flex-start、center、flex-end 以及stretch 注意：要使stretch 选项生效的话，子元素在次轴方向上不能有固定尺寸。
9. TextInput 是一个运行用户输入文本的基础组件。它有一个名为onChangeText 的属性，此属性接受一个函数，而此函数会在文本变化时被调用。另外还有一个名为onSubmintEdit 的属性，会在文本被提交后（用户按下软键盘上的提交键）调用。
10. ScrollView 是一个通用的可滚动的容器，你可以在其中放入多个组件和视图，而且这些组件并不需要是同类型的。ScrollView 不仅可以垂直滚动，还能水平滚动（通过horizontal 属性来设置）
11. ListView 组件用于显示一个垂直的滚动列表，其中的元素之间结构相似而仅数据不同。ListView 更适合长列表数据，且元素个数可以增删。和ScrollView 不同的是，ListView 并不立即渲染所有元素，而是优先渲染屏幕上可见的元素。ListView 组件必须的两个属性是dataSource 和renderRow。dataSource 是列表的数据源，而renderRow 则逐个解析数据源中的数据，然后返回一个设定好格式的组件来渲染。

## 参考文档

1. [reaact native 官网入门文档](https://facebook.github.io/react-native/docs/getting-started.html)
2. [reaact native 中文入门文档](http://reactnative.cn/docs/0.31/getting-started.html#content)
3. [react-natvie 的es5 es6 写法对照](http://bbs.reactnative.cn/topic/15/react-react-native-%E7%9A%84es5-es6%E5%86%99%E6%B3%95%E5%AF%B9%E7%85%A7%E8%A1%A8  )
4. [React Naitve 开发指南](http://makeitopen.com/tutorials/building-the-f8-app/planning/ )
5. [React Naitve 开发指南（中文版）](http://f8-app.liaohuqiu.net/tutorials/building-the-f8-app/planning/ )
