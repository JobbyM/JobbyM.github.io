---
title: Atom 使用手册
date: 2016-11-02 11:18:05
tags:
  - Atom
  - apm
  - 技术
categories: 技术
comments: true
---

## 简介
记录使用Atom 编辑器的相关方法
1. Atom - 一个为21世纪所创造的可配置编辑器
  {% cq %} Atom is a hackable text editor for the 21st century, built on Electron, and based on everything we love about our favorite editors. We designed it to be deeply customizable, but still approachable using the default configuration.{% endcq %}
<!--more-->
2. apm
  {% cq %} apm - Atom Package Manager{% endcq %}


## 使用apm 安装插件
1. 首先需要修改apm 的源
```cmd
$ apm config set registry https://registry.npm.taobao.org/
```
  使用如下命令进行验证是否被正确设置
```cmd
$ apm config get registry
https://registry.npm.taobao.org/
```
  或者是查看<code>~/.atom/.apmrc</code> 文件是否被正确设置
2. 如果安装插件时有防火墙，可以禁用strict ssl
```cmd
$ apm config set strict-ssl false
```
  使用如下命令进行验证是否被正确设置
```cmd
$ apm config get strict-ssl
false
```
  或者是查看<code>~/.atom/.apmrc</code> 文件是否被正确设置
3. apm 相关命令
```cmd
$ apm

apm - Atom Package Manager powered by https://atom.io

Usage: apm <command>

where <command> is one of:
    clean, config, dedupe, deinstall, delete, dev, develop, disable, docs,
    enable, erase, featured, home, i, init, install, link, linked, links, list,
    ln, lns, login, ls, open, outdated, publish, rebuild, rebuild-module-cache,
    remove, rm, search, show, star, starred, stars, test, uninstall, unlink,
    unpublish, unstar, update, upgrade, view.

Run `apm help <command>` to see the more details about a specific command.

选项：
  --color        Enable colored output                                      [boolean] [默认值: true]
  -v, --version  Print the apm version
  -h, --help     Print this usage message
```
4. apm 安装包实例
  为了便于编辑react，安装[react插件](https://orktes.github.io/atom-react/)
```cmd
$ apm install react
```

## 快捷键
1.修改当前文件的类型
Atom会自动识别你当前编辑的文件的类型
如果识别失败,Atom 会将此文件当做普通的文本文档来处理
在这种情况下我们可以使用`Ctrl+Shift+L`来手动指定或改变当前文件的类型

## 参考文档
1. [Atom GitHub 地址](https://github.com/atom/atom)
2. [Atom 官网](https://atom.io/)
3. [Atom 官网](https://atom.io/)
