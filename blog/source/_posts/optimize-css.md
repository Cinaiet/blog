---
title: 前端性能调优-CSS
date: 2025-02-11 14:54:09
categories: JavaScript
tags: 性能优化
---

CSS 性能调优对于提升页面的加载速度和渲染效率非常重要。下面是一些关键的 CSS 调优技巧。

<!--more-->

## 提升 CSS 渲染性能

-   慎用 expensive 属性；如 nth-child 伪类，position:fixed 定位；
-   尽量减少样式层级数； 如 div ul li span i {color: red;}
-   尽量避免使用占用过多 CPU 和内存的属性；如 text-indent:9999px;
-   尽量避免使用耗电量大的属性； 如 CSS3 3D transforms、transitions、Opaticy;

## 合理使用 CSS 选择器

-   尽量避免使用 CSS 表达式； 如 background-color: expression((new Date().getHours() % 2 ? '#FFF': '#000'))
-   尽量避免使用通配符选择器 如 body > a {xxx}
-   尽量避免类正则的属性选择器 如 \*= |= ^= $=

## 提升 CSS 文件加载性能

-   使用外链的 CSS
-   尽量避免使用 @import

## 精简 CSS 代码

-   使用缩写语句；如 margin-top:10px; margin-bottom: 10; 可以合写为 margin: 10px 0;
-   删除不必要的零； 如 font-size:0.5rem; 可以写为 font-size: .5rem;
-   删除不必要的单位； 如 margin-top: 0px; 可写为 margin-top: 0;
-   删除过多分号；
-   删除空格和注释；
-   尽量减少样式表的大小；

## 合理使用 Web fonts

-   将字体部署在 CDN 上；
-   将字体以 base64 形式保存在 CSS 中并通过 localstorage 进行缓存；
-   Google 字体应使用国内托管服务；

## CSS 动画优化

-   避免同时动画；
-   延迟动画初始化；保证其它动画正常渲染
-   结合 SVG；
