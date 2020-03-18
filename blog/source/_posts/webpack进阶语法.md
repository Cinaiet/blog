---
title: webpack进阶语法
categories:
  - JavaScript
tags:
  - webpack
originContent: ''
toc: false
date: 2020-03-18 14:07:08
---

### 自动清理构建目录产物

#### 修改build构建命令
```
// 修改build构建方式命令
$ rm -rf ./dist && webpack 
$ ramraf ./dist && webpack
```
#### clean-webpack-plugin 
引入该插件之后，构建前会先删除output指定的文件目录

```
$ npm i clean-webpack-plugin -D
// webpack.prod.js
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
module.export = {
  plugins: [
    new CleanWebpackPlugin()
  ]
}
```
### 自动补齐css3前缀
css3使用不同的前缀，兼容不同浏览器内核。

浏览器 | 内核 | 前缀
---- | ---- | ----
IE、 | Trident | -ms
fireFox | Geko | -moz
欧朋 | presto | -o
Chrome | Webkit | -webkit
```
// 例
.box {
  -moz-border-radius: 10px;
  -webkit-border-radius: 10px;
  -o-border-radius: 10px;
  border-radius: 10px;
}
```
#### PostCss插件 autoPrefixer自动补齐css前缀
```
// 在解析css的loader中添加 postcss-loader
{
  loader: 'postcss-loader',
  options: {
    plugins: () => {
      require('autoprefixer')({
        // 兼容最后两个版本以上，用户>1% ，iOS 7
        overrideBrowserslist: ['last 2 version', '> 1%', 'ios 7'] 
      })
    }
  } 
}
```
### 移动端css px 转换成 rem
由于移动端设备屏幕宽度都不一样，所以需添加页面适配。

#### @media媒体查询
```
@media screen and(max-width: 480px) {
  ...
}
```
弊端： 需要写多套css去适配

#### rem
W3C对rem的定义: font-size of the root element

**rem 和px的区别**
- rem是相对单位
- px 是绝对单位

#### px2rem-loader
1.  使用px2rem-loader ，页面渲染时计算根元素的font-size

```
$ npm i px2rem-loader -D
$ npm i lib-flexible -S
添加loader
{
  loader: 'px2rem-loader',
  opstions: {
    remUnit: 75, // rem 相对px的单位  1rem = 75px
    remPrecesion: 8, // 保留小数位数
  }
}
```
2. 利用手淘的lib-flexible
  由于lib-flexible是内联静态资源，所以需要手动在html文件中导入。

### 静态资源内联
#### 资源内联的意义
1. 代码层面
    - 页面框架的初始化脚本
    - 上报相关打点
    - css内联避免页面闪动
2. 请求层面
    - 减少 http请求，
    - 小图片或者字体内联(utl-loader)

#### HTML内联
使用raw-loader
```
// 在html head中新增
<script>${require(' raw-loader!babel-loader!. /meta.html')}</script>
```
#### Js内联

raw-loader 内联 JS
```
// 在html head中新增
<script>${require('raw-loader!babel-loader!../node_modules/lib-flexible')</script>
```

#### CSS 内联
1. 借助 style-loader
```
module.exports = { module: {
rules: [
{
  test: /\.scss$/, 
  use: [
    {
      loader: 'style-loader', 
      options: {
        insertAt: 'top', // 样式插入到 <head>
        singleton: true, //将所有的style标签合并成一个 }
    }, 
    "css-loader", "sass-loader"
  ], },
] },
};
```
2. html-inline-css-webpack-plugin


### 多页面应用打包

1. 更改项目文件目录，如图所示
![项目目录结构图示](./public/images/webpack_file_directory.jpg)

### sourcemap

### 提取公共资源









 

