---
title: 一日一练-CSS font-weight的使用
date: 2018-11-06 08:58:00
tags:
  - 技术
  - 一日一练
  - CSS
categories: 技术
---

## 用例
前端开发工程师在根据设计师给定的标注进行设计时，通常会发现，字体通常是是这种样式
```
font-family: PingFang-SC-Medium;
font-size: 14px;
color: #333333;
letter-spacing: 0;
text-align: justify;
line-height: 22px;
```

在开发中，需要根据`font-family` 来使用`font-weight` 来进行替换，替换规则如下：
```
100 - Thin
200 - Extra Light (Ultra Light)
300 - Light
400 - Normal
500 - Medium
600 - Semi Bold (Demi Bold)
700 - Bold
800 - Extra Bold (Ultra Bold)
900 - Black (Heavy)
```

而下面是根据上述规则进行的总结，因为设计师使用苹果系统，所以得出了如下替换规则：
```
PingFangSC-Regular - 400
PingFang-SC-Medium - 500
PingFangSC-Semibold - 600
PingFang-SC-Bold - 700
```

## 字体粗细：font-weight 属性
{% asset_img font-weight_0.png %}
`font-weight` 属性执行字体中字形的重量，这取决于黑度等级或笔画粗细。
其值的意义如下：
**100至900**
这些有序排列中的每个值，表示至少与其起身拥有相同黑度的重量。其大致符合下列通用重量名称：
```
100 - Thin
200 - Extra Light (Ultra Light)
300 - Light
400 - Normal
500 - Medium
600 - Semi Bold (Demi Bold)
700 - Bold
800 - Extra Bold (Ultra Bold)
900 - Black (Heavy)
```
**normal**
  与`400` 相同。
**bold**
  与`700` 相同。
**bolder**
  指定外观的重量大于继承的值。
**lighter**
  指定外观的重量小于继承的值。

通常一个特定的字体家族仅包含少数的可用重量。若一个重量所指定的自行不存在，则应当使用相近重量的字形。通常，较重的重量会映射到更重的重量、较轻的重量会映射到更轻的重量。下面的例子展示了不同重量将使用的外观，灰色表示该重量的外观不存在、使用的是相近重量的字形：
{% asset_img font-weight_1.png %}

值`bolder` 和`lighter` 表示其值相对于其父元素的重量。基于继承的重量值，其重量值，其重量通常是使用下表计算而得的。子元素将继承计算后的宽度，而不是值`bolder` 和`lighter`。
{% asset_img font-weight_2.png %}

## 参考文档
1.[CSS3字体模块 字体粗细`font-weight` 属性](https://www.w3.org/html/ig/zh/wiki/CSS3%E5%AD%97%E4%BD%93%E6%A8%A1%E5%9D%97)
