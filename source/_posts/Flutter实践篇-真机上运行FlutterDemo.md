---
title: Flutter实践篇-真机上运行FlutterDemo
date: 2018-11-21 14:17:27
tags:
---

>子曰：实践出真知

## 前言
了解了一段时间的Flutter，并粗略浏览了Dart 语言，接下来就是上手代码了。

{% asset_img flutter_1.png %}

<!--more-->

## 环境配置
首先看一看环境配置相关
```
系统：Win7 64 位
Android Studio: 3.2.1
真机：RedMi Note2
```

## 构建项目
1.启动Android Studio
2.选择由`Start a new Flutter project` 开始新建一个Flutter 项目
3.首次运行工具栏中的运行按钮（绿色三角形，快捷键为Shift+F10），项目在
```
Resolving dependencies...
```
hangs on了。


## 处理问题
bing.com 上搜索，找到解决方案
```
https://github.com/flutter/flutter/issues/11856
```
```
./flutter_project_name/android/gradlew

After successful build.
Try run the project again.
```
但是，我这里没有成功运行，将上述方案进行了优化。跟着我一步一步进行来处理
1.直接在Android Studio 下面的Terminal 命令行中运行`flutter run -v`
之后，命令长时间停在`hanging on "Resolving dependencies..."`
2.此时可以在Terminal 命令行中键入
```
cd android
```
进入`project_name\android`目录，在此目录上运行下面命令
```
gradlew app:properties -v
```
查看命令运行中产生的信息，可以分析出正在安装`Android SDK 27`（应该是根据相应的手机进行安装，这里是redmi note2）在这里提示错误
```
Warning: An error occurred while preparing SDK package Android SDK Platform 27: archive is not a ZIP archive.:
```
3.于是，我在命令行中输入`android` 启动sdkmanager ，手动选择了`Android SDK  Buid-tools 27 ` 和`Android SDK  Buid-tools 27.0.3` 以及Android 8.0.1（API 27） 下名的`SDK Platform` 和`Sources for Android SDK`（Android 开发小白，不是很清楚上述`Android SDK Platform 27` 的具体含义，于是将可能相关的包都进行了安装）
4.安装成功之后，在Android Studio 3.2.1 中重新运行了
```
flutter run -v
```
在`Resolving dependencies` 手动停止，执行如下命令
```
cd android
gradlew app:properties -v
```
就可以在手机上看到效果了，效果如下：
{% asset_img flutter_0.png %}

## 总结
启动第一个Flutter 项目，还是遇到了许多问题，例如：Android Studio 安装时不能访问`dl.google.*` 相关地址、Dart 和Flutter 插件的安装问题等等、运行`flutter doctor` 诊断时遇到的各种问题。
上述问题基本上都可以网络上找到相应的处理方案，而`hanging on "Resolving dependencies..."` 长时间停留的问题，则是耗时较长才解决的，因此在这里记录下来，希望可以帮助到更多的人。

## 参考文档
1.[hanging on "Resolving dependencies..." github](https://github.com/flutter/flutter/issues/11856)
