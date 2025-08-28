---
title: 前端性能调优-JavaScript
date: 2025-02-11 15:35:12
categories: JavaScript
tags: 性能优化
---

并不是每次项目开发都需要进行优化，而是当项目进行到某一阶段，程序变得越来越复杂时，再进行优化。当需要时再进行优化，书写的同时也应当注意代码的可维护性。

<!--more-->

## 提升加载性能

将 CSS 放在 head 标签里，JS 放在 body 结尾的地方；

## 变量和函数优化

- 尽量使用 id 选择器；
- 尽量避免使用 eval；
- 函数尽可能保持简洁；
- 使用事件节流函数；
- 使用事件委托；

## 动画优化

- 避免添加大量 JS 动画；
- 尽量使用 CSS3 动画；
- 尽量使用 canvas 动画；
- 合理使用 requestAnimationFrame 动画代替 setTimeout、setInterval;requestAnimationFrame 可以在正确的时间进行渲染.

## 合理使用缓存

- 缓存 DOM 对象；
- 缓存列表长度；
- 使用可缓存的 Ajax；
