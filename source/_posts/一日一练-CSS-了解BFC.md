---
title: 一日一练-CSS 了解BFC
date: 2018-06-04 09:57:16
comments: true
tags:
  - 技术
  - 一日一练
  - CSS
  - BFC
categories: 技术
---

{% asset_img hero.png %}

## BFC 的定义
BFC 全称为block formatting context，中文为“块级格式化上下文”。相对应的还有IFC，也就是inline formatting context，中文为“内联格式化上下文”

关于BFC 的特性可以用“CSS 世界的结界” 这种称谓概括BFC 的特性。“结界” 指通过一些特定的手段形成的封闭空间，里面的人出不去，外面的人进不来，具有极强的防御力。BFC 的表现如出一辙。

<!--more-->

请记住这个原则：**如果一个元素具有BFC，内部子元素再怎么翻江倒海、翻云覆雨，都不会影响外部的元素。** 所以BFC 元素是不可能发生margin 重叠的，因为margin 重叠是会影响外面的元素的；BFC 元素也可以用来清除浮动的影响，因为如果不清除，子元素浮动则父元素高度塌陷，必然会影响后面元素布局和定位，这显然有违BFC 元素的子元素不会影响外部元素的设定。

能够触发BFC 的情况如下：
* `<html>` 根元素；
* float 的值不为none；
* overflow 的值为auto、scroll 或hidden；
* display 的值为table-cell、table-caption 和inline-block 中的任何一个；
* position 的值不为relative 和static。

换言之，只要元素符合上面任意一个条件，就无需使用clear:both 属性去清除浮动的影响了。因此，不要见到一个`<div>` 元素就加个类似`.clearfix` 的类名，否则只能暴露你孱弱的CSS 基本功。

---
## BFC 与流体布局
BFC 的结界特性最重要的用途其实不是去margin 重叠或者是清除float 影响，而是实现更健壮、更智能的自适应性布局。

从最基本的文字环绕效果说起：
```html
<div class="father">
  <img src="avatar.png" alt="">
  <p class="animal">小猫1，小猫2，小猫3，小猫4，小猫5，小猫6，小狗1，小狗2，小狗3，小狗5</p>
</div>
```
```css
img { float: left;}
```
{% asset_img bfc_0.png %}

如图所示，此时.animal 的内容显然受到了设置了float 属性值的图片的影响而被环绕了。此时如果我们给.animal 元素设置具有BFC 特性的属性，如overflow:hidden, 如下：
```css
.animal { overflow: hidden; }
```

则根据BFC 的表现原则，具有BFC 特性的元素的子元素不会受到外部元素影响，也不会影响外部元素。于是，这里的.animal 元素为了不和浮动元素产生任何交集，顺着浮动边缘形成自己的封闭上下文，如图所示：
{% asset_img bfc_1.png %}

也就是说，普通流体元素在设置了overflow:hidden 后，会自动填满容器中除了浮动元素以外的剩余空间，形成自适应布局效果，而且这种自适应性布局要比纯流体自适应更加智能。比如，我们让图片的尺寸变大或变小，右侧自适应内容无须更改任何样式代码，都可以自动填满剩余空间。

实际项目开发中，图片和文字不可能靠这么近，如果想要保持合适的间距，那也很简单，如果元素是左浮动，则浮动元素可以设置margin-right 或透明border-right 或padding-right；又或者右侧BFC 元素设置成透明border-left 或者padding-left，但不包括margin-left，因为如果想要使用margin-left，则其值必须是浮动元素的宽度加间隙的大小，就变成动态不可控的了，无法大规模复用。因此，套用上面例子的HTML，假设我们希望间隙是10px，则下面几种写法都是可以的：
* img { margin-right: 10px; }
* img { border-right: 10px solid transparent; }
* img { padding-right: 10px; }
* .animal { border-left: 10px solid transparent; }
* .animal { padding-left: 10px; }

一般而言，我喜欢通过在浮动元素上设置margin 来控制间距，也就是下面的CSS 代码：
```css
img {
  float: left;
  margin-right: 10px;
}
.animal {
  overflow: hidden;
}
```

和基于纯流体特性实现的两栏或多栏自适应布局相比，基于BFC 特性的自适应布局有如下优点。
（1）自适应内容由于封闭而更健壮，容错性更强。比方说，内部设置clear:both 不会与float 元素相互干扰而导致错位。
（2）自适应内容自动填满浮动以外区域，无须关心浮动元素宽度，可以整站大规模应用。比方说，抽象几个通用的布局类名，如：
```css
.left { float: left; }
.right { float: right; }
.bfc { overflow: hidden; }
```

于是只要遇到两栏结构，直接使用上面的结构类名就可以完成基本的布局。HTML 示意如下：
```html
<div class="bfc">
  <img src="avatar.png" alt="">
  <p class="bfc">小猫1，小猫2，小猫3，小猫4……</p>
</div>
```

最后，我们可以提炼出两套IE7 以上版本浏览器适配的自适应解决方案。
（1）借助overflow 属性，如下，
```css
.lbf-content { overflow: hidden; }
```
（2）融合display:table-cell 和display:inline-block，如下：
```css
.lbf-content {
  display: table-cell; width: 9999px;
  /* 如果不需要兼容IE7， 下面样式可以省略  */
  *display: inline-block; *width: auto;
}
```

这两套基于BFC 的自适应方案均支持无限嵌套，因此，多栏自适应可以通过嵌套方式实现。这两种方案均有一点不足，前者如果子元素要定位到父元素的外面可能会被隐藏，后者无法直接让连续英文字符换行。

最后，关于display:table-cell 元素内连续英文字符无法换行的问题，事实上可以解决的，就是使用类似下面的CSS 代码：
```css
.word-break {
  display: table;
  width: 100%;
  table-layout: fixed;
  word-break: break-all;
}
```


---
## 参考文档
1.《CSS 世界》  张鑫旭 6.3 CSS世界的结界
