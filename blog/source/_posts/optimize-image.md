---
title: 前端性能调优-图片优化
date: 2025-02-10 11:06:00
categories: JavaScript
tags: 性能优化
---

图片优化，指的是在不牺牲图片质量的前提下，尽可能压缩图片大小，从而达到缩短页面加载时长的目的。同时，图片优化也有助于 SEO，提高图片在搜索引擎上的排名。

<!--more-->

## 图片格式及应用场景

### JPEG (Joint Photographic Experts Group)

联合图像专家小组针对彩色图片而广泛使用的有损压缩图形格式。

-   栅格图形。常用文件扩展名为 .jpg，也有.jpeg、.jpe。JPEG 在互联网上应用于存储和传输照片；
-   不适合线条图形和文字、图标图形，压缩算法不太适合这类型的图形，并且不支持透明度；
-   非常适合颜色丰富的照片、彩色图、大焦点图、Banner 图、结构不规则的图形。

### PNG (Portable Network Graphics)

便携式网络图形是一种无损压缩的位图图形格式，支持索引、灰度、RGB 三种颜色方案以及 Alpha 通道等特性。

-   栅格图形。PNG 最初是作为替代 GIF 来设计的，能够显示 256 色，文件比 JPEG 或者 GIF 大，但是 PNG 能非常好的保留图像质量。支持 Alpha 通道的半透明和透明特性。最高支持 24 位彩色图像(PNG-24)和 8 位灰度图像(PNG-8)
-   不适合彩色图像；
-   非常适合 纯色、透明、线条绘图，图标；边缘清晰、有大块相同颜色区域；颜色数较少但需要半透明的图像。

### GIF (Graphics Interchange Format)

图像互换格式是一种位图图形文件格式，以 8 位色(即 256 种颜色)重现真彩色的图像，采用 LAW 压缩算法进行编码。

-   栅格图形。 支持 256 色；仅支持完全透明和完全不透明；如果需要比较通用的动画，GIF 是唯一选择；
-   不适合存储彩色图片，因为每个像素只有 8 比特；
-   适合用于 动画、图标。

### Webp

Webp 是一种现代图像格式，可为图像提供无损压缩和有损压缩，这使得它非常灵活。

-   能同时保证一定程序上的图像质量和比较小的体积。可以插入多帧，实现动画效果；可以设置透明度；采用 8 位压缩算法。无损的 Webp 比 PNG 小 26%，有损的 Webp 比 JPEG 小 25%-34%。比 GIF 有更好的动画。
-   不适合彩色图片，最多处理 256 色；
-   适用于图形和半透明图像。

## 图片压缩

### 压缩 PNG

使用 [node-pngquant-native](https://github.com/oxUnd/node-pngquant-native) 进行压缩，压缩 PNG24 非常好，跨平台且压缩比高。

```
var pngquant = require('node-pngquant-native');

fs.readFile('./input.png', function (err, buffer) {
  if (err) throw err;
  var resBuffer = pngquant.compress(buffer, {
    "speed": 1 //1 ~ 11
  });

  fs.writeFile('./output.png', resBuffer, {
      flags: 'wb'
  }, function(err){});
});

```

### 压缩 GIF

使用[Gifsicle](https://www.lcdf.org/gifsicle/)通过改变每帧比例，减少 gif 文件大小，同时可以使用透明来达到更小的文件大小，目前公认的解决方案。

#### 使用方式

-   gifsicle --optimize=3 - o output.gif input.gif (优化级别设置为不小于 2,1 的话基本不压缩)

-   将透明部分截去: gifsicle --optimize=3 --crop-transparency -o output.gif input.gif

## 响应式图片加载

-   通过 Js 检测窗口大小来修改图片大小；
-   CSS 媒体查询

    ```
    @media screen and(max-width:640px) {
        image {
            width: 50%;
        }
    }
    ```

-   img 标签属性

    ```
    <img srcset="img-320w.jpg,img-640w.jpg 2x,img-960w.jpg 3x" src="img-960w.jpg" alt="img" />

    // x描述符标识图像设备的像素比
    ```

## 图像逐步加载

### 统一占位符

如图像加载过程中使用固定的 loading 图；

### LQIP

使用[LQIP](https://github.com/zouhir/lqip)低质量图像占位符

```
const lqip = require('lqip');

const file = `./dest/to/file/zouhir-riding-a-bike.jpg`;

lqip.base64(file).then(res => {
  console.log(res); // "data:image/jpeg;base64,/9j/2wBDAAYEBQYFBAYGBQYHBwYIChAKCgkJChQODwwQFxQYGBcUFhY.....
});

```

### SQIP

基于 SVG 的图像占位符 [SQIP](https://github.com/axe312ger/sqip)

```
const sqip = require('sqip');

const result = sqip({
    filename: 'input.png',
    numberOfPrimitives: 10 // 效果值
});

console.log(result);

```

## 图片服务器智能优化

根据图片 URL 连接上的特殊参数服务器自动生成不同格式、大小、质量的图片。

如将上述不同的压缩图像的工具部署到服务器，采用不同的入参来控制不同的压缩形式来生成相应的图像。
