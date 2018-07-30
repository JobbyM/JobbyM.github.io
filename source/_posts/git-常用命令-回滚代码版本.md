---
title: git 常用命令 回滚代码版本
date: 2018-07-30 13:33:48
tags:
  - Git
  - 技术
categories: 技术
comments: true
---

> 子曰：git 常用命令 回滚代码版本

0x00：
查看代码历史提交记录
```
git log
```

0x01：
回滚代码到上一个版本
```
git reset --hard HEAD^
```

0x02:
回滚到指定版本
```
git reset --hard commit_id  
```
其中`commit_id` 为git 提交记录时，git使用SHA1 计算出来的一个版本号

<!--more-->

0x03:
在windows的cmd控制台下操作git，想要回滚到上一次提交，但是输入`git reset --hard HEAD^`后就显示`more?`，多按几次回车后就报错如下，如何解决呢？
```
More?
More?
fatal: ambiguous argument 'HEAD
': unknown revision or path not in the working tree.
Use '--' to separate paths from revisions, like this:
'git <command> [<revision>...] -- [<file>...]'
```

这是因为cmd控制台中换行符默认是`^`，而不是`\ `，所以它的`more？`的意思是问你下一行是否需要再输入，而`^` 符号就被当做换行符而被git命令忽略掉了。

解决方法有如下几种：
加引号：`git reset --hard "HEAD^"`
加一个`^`：g`it reset --hard HEAD^^`
换成`~`：`git reset --hard HEAD~` 或者 `git reset --hard HEAD~1`
`~`后面的数字表示回退几次提交，默认是一次

当然还可以换成git bash，powershell等就不会出现这种问题了

0x04:
参考文档
1.[git reset --hard HEAD^后显示more?的解决方案](https://blog.csdn.net/qq_32623363/article/details/78968077)
