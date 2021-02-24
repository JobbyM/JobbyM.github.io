---
title: 一日一练-CSS 了解 object-fit 属性
date: 2021-02-23 16:22:14
comments: true
tags:
  - 技术
  - CSS
  - object-fit
  - object-position
categories: 技术
---

> 子曰：一日一练-CSS 了解 `object-fit` 属性 和 `object-position`

## 问题
平常开发中会遇到在一个固定大小的容器中满屏保持比例展示不固定大小比例的的图片。

<!--more-->

1. 如果图片的 `width` 和 `height` 均小于 box 的 `width` 和 `height`，那么直接让图片上下左右居中即可
2. 如果图片的 `width` 大于 box 的 `width` ，`height` 小于 box 的 `width` 和 `height`， 让图片按照宽度 `width` 等比缩放，然后垂直居中
3. 如果图片的 `width` 小于 box 的 `width` ，`height` 大于 box 的 `width` 和 `height`， 让图片按照高度 `height` 等比缩放，然后左右居中
4. 如果图片的 `width` 和 `height` 均大于 box 的 `width` 和 `height`，那么分别计算图片的 `width` 和 `height` 和 box 的 `width` 和 `height` 的比，用较小的值作为缩放的比例，然后上下左右居中即可。

## 解决方法
使用 CSS 的 `object-fit` 属性结合 CSS 的 `object-position` 属性
```css
.img {
  width: 200px;
  height: 200px;
  object-fit: none;
  object-position: 50% 50%;
}
```

## object-fit
`object-fit` 属性指定元素的内容如何去适应指定容器的高度和宽度
`object-fit` 一般用于 `img` 和 `video` 标签，一般可以对这些元素进行保留原始比例的剪切、缩放或者直接进行拉伸。
您可以通过使用 `object-position` 属性来切换被替换元素的内容对象在元素框内的对齐方式。

### 语法
```
object-fit: fill|contain|cover|scale-down|none|initial|inherit;
```

#### 属性值
|  值   | 描述  |
|  ----  | ----  |
| fill  | 默认，不保证保持原有比例，内容拉伸填充整个内容容器。 |
| contain  | 保持原有尺寸比例。内容被缩放。 |
| cover  | 保持原有尺寸比例。但部分内容可能被裁剪。 |
| none  | 保持原有元素内容的长度和宽度，也就是说内容不会被重置。 |
| scale-down  | 保持原有尺寸比例。内容的尺寸与 none 或 contain 中的一个相同，取决于它们两个之剑谁得到的对象尺寸会更小一些。 |
| initial  | 设置为默认值。 |
| inherit  | 从该元素的父元素继承属性。 |

## object-position
`object-position` 属性一般与 `object-fit` 一起使用，用来设置元素的位置。
`object-fit` 一般用于 `img` 和 `video` 标签。

### 语法
```
object-position: position|initial|inherit;
```

#### 属性值
|  值   | 描述  |
|  ----  | ----  |
| position  | 指定 `image` 或 `video` 在容器中的位置。第一个值为 x 坐标位置的值，第二个值为 y 坐标位置的值。表示的方式有：<br>```object-position: 50% 50%;```<br>```object-position: right top;```<br>```object-position: left down;```<br>```object-position: 250px 125px;``` |
| initial  | 设置为默认值。 |
| inherit  | 从该元素的父元素继承属性。 |


## 参考文档
1. [CSS object-fit 属性 RUNOOB.COM](https://www.runoob.com/cssref/pr-object-fit.html)
1. [CSS object-position 属性 RUNOOB.COM](https://www.runoob.com/cssref/pr-object-position.html)

