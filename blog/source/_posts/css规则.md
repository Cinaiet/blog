---
title: css规则
categories:
  - CSS
tags:
  - 如果当时
originContent: ''
toc: false
date: 2020-06-19 18:15:52
---

CSS 的顶层样式表由两种规则组成的规则列表构成，一种被称为 at-rule，也就是 at 规则，另一种是 qualified rule，也就是普通规则。

<!--more -->

### at-rule
at-rule 由一个 @ 关键字和后续的一个区块组成，如果没有区块，则以分号结束。

#### @charset
@charset 用于提示 CSS 文件使用的字符编码方式，它如果被使用，必须出现在最前面。这个规则只在给出语法解析阶段前使用，并不影响页面上的展示效果。
```
@charset "utf-8";
```

#### @import
@import 用于引入一个 CSS 文件，除了 @charset 规则不会被引入，@import 可以引入另一个文件的全部内容。

```
@import "mystyle.css";
@import url("mystyle.css");
```
#### @media

media 就是大名鼎鼎的 media query 使用的规则了，它能够对设备的类型进行一些判断。在 media 的区块内，是普通规则列表
```
@media print {
    body { font-size: 10pt }
}
```
#### @page
page 用于分页媒体访问网页时(如打印)的表现设置，页面是一种特殊的盒模型结构，除了页面本身，还可以设置它周围的盒。
```

@page {
  size: 8.5in 11in;
  margin: 10%;
  @top-left {
    content: "Hamlet";
  }
  @top-right {
    content: "Page " counter(page);
  }
}
```
#### @ counter-style
counter-style 产生一种数据，用于定义列表项的表现。如可以自定义counter的样式。

```
@counter-style triangle {
  system: cyclic;
  symbols: ‣;
  suffix: " ";
}
```
#### @ key-frames
keyframes 产生一种数据，用于定义动画关键帧。
```
@keyframes diagonal-slide {
  from {
    left: 0;
    top: 0;
  }
  to {
    left: 100px;
    top: 100px;
  }
}
```
#### @ fontface
fontface 用于定义一种字体，icon font 技术就是利用这个特性来实现的。
```
@font-face {
  font-family: Gentium;
  src: url(http://example.com/fonts/Gentium.woff);
}
p { font-family: Gentium, serif; }
```

#### @ support
support 检查环境的特性，它与 media 比较类似。

#### @ namespace
用于跟 XML 命名空间配合的一个规则，表示内部的 CSS 选择器全都带上特定命名空间。
#### @ viewport
用于设置视口的一些特性，不过兼容性目前不是很好，多数时候被 HTML 的 meta 代替。
#### 其他
- @color-profile 是 SVG1.0 引入的 CSS 特性，但是实现状况不怎么好。
- @document 还没讨论清楚，被推迟到了 CSS4 中。
- @font-feature-values 。

### 普通规则
普通规则主要是由选择器和声明区块构成。声明区块又由属性和值构成。

#### 选择器
任何选择器，都是由几个符号结构连接的：空格、大于号、加号、波浪线、双竖线，这里需要注意一下，空格，即为后代选择器的优先级较低。

#### 声明：属性和值
声明部分是一个由“属性: 值”组成的序列。

**属性**是由中划线、下划线、字母等组成的标识符，CSS 还支持使用反斜杠转义。我们需要注意的是：属性不允许使用连续的两个中划线开头，这样的属性会被认为是 CSS 变量。

**值**的部分，根据每个 CSS 属性可以取到不同的值，这里的值可能是字符串、标识符

CSS 属性值可能是以下类型。

- CSS 范围的关键字：initial，unset，inherit，任何属性都可以的关键字。
- 字符串：比如 content 属性。
- URL：使用 url() 函数的 URL 值。
- 整数 / 实数：比如 flex 属性。
- 维度：单位的整数 / 实数，比如 width 属性。
- 百分比：大部分维度都支持。
- 颜色：比如 background-color 属性。
- 图片：比如 background-image 属性。
- 2D 位置：比如 background-position 属性。
- 函数：来自函数的值，比如 transform 属性。

CSS 支持的特定计算型函数：

- calc()  支持加减乘除四则运算。
```
section {
  float: left;
  margin: 1em; border: solid 1px;
  width: calc(100%/3 - 2*1em - 2*1px);
}
```
- max()  取两数中较大的一个
- min() 取两数中较小的一个
- clamp()  给一个值限定一个范围，超出范围外则使用范围的最大或者最小值。
- toggle()  在规则选中多于一个元素时生效，它会在几个值之间来回切换，比如我们要让一个列表项的样式圆点和方点间隔出现:
```
ul { list-style-type: toggle(circle, square); }
```
- attr()  允许 CSS 接受属性值的控制。


