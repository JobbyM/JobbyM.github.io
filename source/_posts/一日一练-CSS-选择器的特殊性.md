---
title: 一日一练-CSS 选择器的特殊性
date: 2018-07-02 16:52:06
comments: true
tags:
  - 技术
  - 一日一练
  - CSS
  - CSS特殊性
  - 翻译
categories: 技术
---

> 子曰：更好的确认CSS 的最终规则
> 参考《CSS 权威指南》第3 章 结构和层叠 特殊性

在实际工作中，总会遇到一个元素可以使用两个或多个规则来选择，每一个规则都有自己的选择器。
假设下面每一对规则都匹配同样的元素，由于所匹配的元素只能是某一种颜色（二选一），如何确认那一个规则更强呢？
```css
h1 { color: red; }
body h1 { color: green; }
```
```css
h2.grape { color: purple; }
h2 { color: silver; }
```
```css
html > body table tr[id="totals"] td ul > li { color: maroon; }
li#answer { color: green; }
```

<!--more-->

解决的方法就是每个选择器的特殊性（specificity）。对于每个规则，用户代理会计算选择器的特殊性，并将这个特殊性附加到规则中的各个声明中。如果一个元素有两个或多个冲突的声明，那么有最高特殊性的声明就会胜出。

选择器的特殊性由选择器本身的组件确定的。特殊性值表述为4 个部分，如：`0，0，0，0`。
一个选择器的具体特殊性如下确定：
* 对于选择器中给定的各个ID 属性值，加`0，1，0，0`
* 对于选择器中给定的各个类属性值、属性选择器或伪类，加`0，0，1，0`。
* 对于选择器中给定的各个元素和伪元素，加`0，0，0，1`。伪元素是否具有特殊性？在这方面CSS2 有些自相矛盾，不过CSS2.1 很清楚地指出，伪元素有特殊性，而且特殊性为`0，0，0，1`.
* 结合符和通配选择器对特殊性没有任何贡献。

根据这个规则，计算文章开头的几组规则的特殊性
```css
h1 { color: red; }        /* specificity = 0,0,0,1 */
body h1 { color: green; } /* specificity = 0,0,0,2 (winner)*/
```
```css
h2.grape { color: purple; } /* specificity = 0,0,1,1 (winner)*/
h2 { color: silver; }       /* specificity = 0,0,0,1 */
```
```css
html > body table tr[id="totals"] td ul > li { color: maroon; } /* specificity = 0,0,1,7 */
li#answer { color: green; } /* specificity = 0,1,0,1 (winner)*/
```

上面已经指出每组规则中的胜出规则
**注意**
1. 因为值是从左向右排序的。特殊性值`1，0，0，0` 大于以0 开头的所有特殊性值，而不论后面的数是什么。所以特殊性值`0，0，1，7` 小于`0，1，0，1`。
2. 每个内联声明的特殊性都是`1，0，0，0`。也就是说内联声明的特殊性最高。
3. 重要声明，即在声明的结束分号之前插入`!important` 比上述的声明的特殊性都高。
