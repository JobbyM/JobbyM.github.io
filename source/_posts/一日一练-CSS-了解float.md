---
title: 一日一练-CSS 了解float
date: 2018-06-04 09:53:17
comments: true
tags:
  - 技术
  - 一日一练
  - CSS
  - float
categories: 技术
---

{% asset_img hero.png %}
## float 的本质
float 属性是一个年代非常久远的属性。float 属性设计的目的是什么呢？一句话： **浮动的本质就是为了实现文字环绕效果**。而这种文字环绕主要指的就是文字环绕图片显示的效果。

<!--more-->

---

## float 的特质
### 包裹性
所谓“包裹性”，由“包裹” 和“自适应性” 两部分组成。

（1）包裹。假设浮动元素父元素宽度为200px，浮动元素子元素是一个128px 宽度的图片，则此时浮动元素宽度表现为“包裹”，就是里面的图片宽度为128px，代码如下：
```css
.father { width: 200px; }
.float { float: left; }
.float img { width: 128px; }
```
```html
<div class="father">
  <div class="float">
    <img src="1.jpg" alt="">
  </div>
</div>
```
（2）自适应性。如果浮动元素的子元素不只是一张128px 宽度的图片，还有一大波普通的文字，例如：
```html
<div class="father">
  <div class="float">
    <img src="1.jpg" alt="">如果浮动元素的子元素不只是一张128px 宽度的图片，还有一大波普通的文字
  </div>
</div>
```

则此时浮动元素宽度就自适应父元素的200px 宽度，最终的宽度表现也是200px。

当然，要想最大宽度自适应父元素宽度，一定是在浮动元素的“首选最小宽度” 比父元素的跨度要小的前提下，比方说上面示意的文字全是一连串超长的英文字母，则浮动元素的宽度显然就不是200px 了。

### 块状化并格式化上下文
块状化的意思是，元素一旦float 的属性值不为none，则其display 计算值就是block 或table。举个例子，打开浏览器控制台，输入如下JavaScript 代码：
```js
var span = document.createElement('span')
document.body.appendChild(span)
console.log('1. ' + window.getComputedStyle(span).display)
// 设置元素左浮动
span.style.cssFloat = 'left'
console.log('2. ' + window.getComputedStyle(span).display)
```
结果如图
{% asset_img float_0.png %}

因此没有任何理由出现下面的组合：
```css
span {
  display: block; // 多余
  float: left;
}
span {
  float: left;
  vertical-align: middle; // 多余
}
```

也不要指望使用text-align 属性控制浮动元素的左右对齐，因为text-align 对块级元素是无效的。

### 破坏文档流
float 特性的精髓 -- “破坏文档流”，这可以说是float 属性的万恶之源，但也是float 属性的立命之本，是其作用机制之所在。

### 没有任何margin 合并

---

## float 的作用机制
float 属性有个著名的特性表现，就是会让父元素的高度塌陷，大多数场景下，这种特性会影响“正常的” 布局，这里我特意把“正常的” 三个字加了引号，因为站在CSS 属性的角度讲，我们希望的结果反而是一种不正常，高度塌陷才是正常。

float 属性的原本作用“只是为了实现文字环绕效果”，而float 属性让父元素塌陷的原因就是为了实现文字环绕效果。

然而，“高度塌陷” 只是让跟随的内容可以和浮动元素在一个水平线上，但这只是实现“环绕效果” 的条件之一，要想实现真正的“环绕效果”，就需要另外一个平时大家不太在意的特性，那就是“行框盒子和浮动元素的不可重叠性”，也就是“行框盒子如果和浮动元素的垂直高度有重叠，则行框盒子在正常定位状态下只会跟随浮动元素，而不会发生重叠”。

注意，这里说的是“行框盒子”，也就是每行内联元素所在的那个盒子，而非外部的块状盒子。实际上，由于浮动元素的塌陷，块状盒子是和图片完全重叠的。

## float 更深入的作用机制
首先，我们需要了解两个和float 相关的术语，一是“浮动锚点”（float anchor），二是“浮动参考”（float reference）。
1. 浮动锚点是float 元素所在的“流” 中的一个点，这个点本身并不浮动，就表现而言更像一个没有margin、border 和padding 的空的内联元素。
2. 浮动参考指的是浮动元素对齐参考的实体。

在CSS 世界中，float 元素的“浮动参考” 是“行框盒子”，也就是float 元素在当前“行框盒子” 内定位。再强调一遍，是“行框盒子”，不是外面的包含块盒子之类的东西，因为CSS 浮动设计的初衷仅仅是实现文字环绕效果。

---

## float 与流体布局
我们可以利用float 破坏CSS 正常流的特性，实现俩栏或多栏的自适应布局。
```css
.father { overflow: hidden; }
.father > img { width: 60px; height: 54px; float: left; }
.animal { margin-left: 70px; }
```
```html
<div class="father">
  <img src="1.jpg" alt="">
  <p class="animal">小猫1，小猫2，小猫3 。。。</p>
</div>
```

{% asset_img float_4.png %}

原理很简单，animal 元素没有浮动，也没有设置宽度，因此，流动性保持很好，设置maring-left/border-left/padding-left 都可以自动改变content box 的尺寸，继而实现了宽度自适应布局效果。

多栏布局
```css
.prev { float: left; }
.next { float: right; }
.title { margin: 0 70px; text-align: center;}
```
```html
<div class="box">
  <a href="" class="prev">&laquo; 上一章</a>
  <a href="" class="next">下一章 &raquo; </a>
  <h3 class="title">一日一练-CSS 了解float</h3>
</div>
```

{% asset_img float_5.png %}
也就是说，title 所在的`<h3>` 标题元素直接左右margin， 借助流体特性，保证不会和两个文字连接重叠。

---

## float 天然克星clear
### 什么是clear 属性
CSS 有一个专门用来处理float 属性带来的高度塌陷等问题的属性，这个属性就是clear。其语法如下：
```
clear: none | left | right | both
```

官方对clear 属性的解释是：“元素盒子的边不能和前面的浮动元素相邻。”

就是设置了clear 属性的元素自身如何如何，而不是让float 元素如何如何。因为，我对clear 属性的理解是下面这样的。
* none：默认值，左右浮动来就来。
* left：左侧抗浮动。
* right：右侧抗浮动。
* both： 两侧抗浮动。

凡是clear:left 或clear:right 起作用的地方，一定可以使用clear:both 替换。

举个例子，假设容器宽度足够，有10 个`<li> `元素，设置了如下CSS 代码：
```css
li {
  width: 20px; height: 20px;
  margin: 5px;
  float: left;
}
li:nth-of-type(3) {
  clear: both;
}
```

也就是说，第三个`<li>` 设置了clear:both，请问表现是怎样的？或者这么说吧：列表最后是1 行显示、2 行显示，还是3 行显示呢？

我们很容易被both 这个单词误导，因为其字面意思是“同时”，所以很多人会认为是3 行，但实际上只会显示2 行，如下图所示：
{% asset_img float_1.png %}

原因在于，clear 属性是让自身不能和前面的浮动元素相邻，注意这里“前面的” 3 个字，也就是clear 属性对“后面的” 浮动元素是不闻不问的，因此才2 行显示而不是3 行。

### 成事不足败事有余的clear
clear 属性只有块级元素才有效的，而::after 等伪元素默认都是内联水平，这就是借助伪元素清除浮动影响时需要设置display 属性值的原因。
```css
.clear:after {
  content: '';
  display: table; // 也可以是‘block’，或者是‘list-item’
  clear:both;
}
```
然而利用伪元素或直接使用下面HTML，有时候也会产生一些意想不到的问题：
```html
<div class="clear:both"></div>
```

实例代码如下：
```html
<div class="father">
  <img src="avatar.png" alt="">
  小猫1，小猫2，
  <div class="clear"></div>
  小猫3，小猫4，小猫5，小猫6，小狗1，小狗2，小狗3，小狗5
</div>
```

下图就是clear:both 导致自适应布局错位示意
{% asset_img float_2.png %}

由于clear:both 的作用本质是让自己不和float 元素在一行显示，并不是真正意义上的清除浮动，因此float 元素一些不好的特性依然存在，于是，会有类似下面的现象。
（1）如果clear:both 元素前面的元素就是float 元素，则clear:both 元素的margin-top 负值即使设成-9999px，也不见任何效果。
（2）clear:both 后面的元素依旧可能会发生文字环绕的现象。举个例子，如下HTML 和CSS：
```html
<div class="father">
  <img src="avatar.png" alt="">
  小猫3，小猫4，小猫5，小猫6，小狗1，小狗2，小狗3，小狗5
</div>
<div>clear:both 后面的元素依旧可能会发生文字环绕的现象。</div>
```
```css
.father:after {
  content: '';
  display: table;
  clear: both;
}
.father img {
  float: left;
  width: 100px; height: 100px;
}
.father + div {
  margin-top: -2px;
}
```

虽然.father 父元素的最后设置了clear:both 来阻止浮动对后面元素的影响，但是最后结果错位依然发生了，如下图所示。
{% asset_img float_3.png %}

---

## 参考文档
1.《CSS 世界》 张鑫旭 6.1 魔鬼属性float
2.[CSS 深入理解之float 浮动](https://segmentfault.com/a/1190000014554601)
