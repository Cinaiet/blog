---
title: JSX的实现原理
categories:
  - JavaScript
tags:
  - 核心原理
toc: false
date: 2021-07-25 09:49:23
---

JSX是React框架背后的核心机理，它使得我们可以在Javaascript中直接书写html。

本文将由JSX展开实现一个简化版的simpleReact。

<!--more-->

## init

```
$ npm init -y // 初始化当前项目
$ npm i webpack webpack -D // 打包配置
```

## webpack

webpack 是一个前端打包工具，支持各种自定义loader，plugin。
使用方法详见 [webpack 基础用法](https://blog.wangguanwei.com/2020/03/14/webpack%E5%9F%BA%E7%A1%80%E7%94%A8%E6%B3%95/)

## babel

babel 是一个将高版本的js，编译成老版本Js的一种工具。

babel在webpack中是以loader的形式存在的。
babel本身是一个不带任何配置的一个核心，`@babel/preset-env`是一个配置好了的、比较常见的（如es6 -> es5）转换配置包

```
$ npm i @babel/core @babel/preset-env babel-loader -D
```

## 编写webpack 配置文件

```
// webpack.config.js

const path = require('path')

module.exports =  {
  entry: './main.js',
  output: {
    filename: 'bundle.js',
    path: path.join(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env'],
            
          }
        },

      },
    ]
  },
  mode: 'development',
  optimization: {
    minimize: false
  }
}
```

## @babel/plugin-transform-react-jsx

在常规的babel preset配置中是不包含JSX的转换的，所以需要借助单独的插件

```
$ npm i @babel/plugin-transform-react-jsx -D
```

修改webpack配置

```
// webpack.conf.js
// ...others
options: {
            presets: ['@babel/preset-env'],
            plugins: [
              "@babel/plugin-transform-react-jsx",
            ]
          }
```

## 书写

当在js中书写jsx语法，使用webpack打包之后的内容如下：

```
// main.js

let x = <div />

// build.js 打包之后的js

var x = /*#__PURE__*/React.createElement("div", null);

// 浏览器解析js报错

// Uncaught ReferenceError: React is not defined
```

这是由于babel-jsx翻译之后生成的js为`React.createElement(ele)`函数，而`React.createElement`
在当前是一个未定义的状态。

### @babel/plugin-transform-react-jsx 扩展

`@babel/plugin-transform-react-jsx` 支持传入的第二个参数为当前的函数名，如`React.createElement`。

修改webpack.conf.js

```
plugins: [
              [
                "@babel/plugin-transform-react-jsx",
                {
                  pragma: "wgwCreateElement"
                }
              ]
            ]
```

再使用webpack打包jsx转换之后：

```
var x = wgwCreateElement("div", null);
```

即函数名变为了自定义的函数名。

### 编写自定义函数

```
window.a = <div class="class" id="id" >
  <div>123</div>
  <div></div>
  <div></div>
</div>
// 打包之后的window.a 

window.a= wgwCreateElement("div", {
  "class": "class",
  id: "id"
}, wgwCreateElement("div", null, "123"), wgwCreateElement("div", null), wgwCreateElement("div", null));

// 由此可得
function wgwCreateElement(tagName, attributes, ...childrens) {
  let ele = document.createElement(tagName)
  for(let attr in attributes) {
    ele.setAttribute(attr, attributes[attr])
  }

  for(let child of childrens) {
    if(typeof child === 'string') {
      child = document.createTextNode(child)
    }
    ele.appendChild(child)
  }
  return ele
}
```

## JSX组件机制

在jsx中，如果tagName 为小写，那么就会认为是一个原生的html标签，如果是大写开头，那么就会认为是一个组件。

如改写main.js

```
window.a = <MyComponent class="class" id="id" >
  <div>123</div>
  <div></div>
  <div></div>
</MyComponent>

// 打包翻译转换后
wgwCreateElement(MyComponent, {
  "class": "class",
  id: "id"
}, wgwCreateElement("div", null, "123"), wgwCreateElement("div", null), wgwCreateElement("div", null));
// 此时MyComponent处于未定义状态
```

### 编写MyComponent

代码不想赘述了，详见 github [main.js](https://github.com/Cinaiet/toy-react/blob/two/main.js)
[toy-react.js](https://github.com/Cinaiet/toy-react/blob/two/toy-react.js)

## 补充

### jsx编译方式

#### 运行时编译（React Automatic Runtime）

当pagram参数值为wgwCreateElement，所以代码编译时会自动解析jsx并调用wgwCreateElement方法。所以定义了pagram之后函数不需要去调用

#### 手动引入 （React Classic Runtime）

**为什么render方法里父节点要接收一个component.root作为参数而不是component？**

在运行编jsx译时，调用wgwCreateElement方法后会实例化一个Component对象，该对象初始root为null，从而会调用render方法（即MyComponent中的render方法），该方法返回一个JSX，从而又会调用createElement方法，此时是一个真实的DOM节点，所以会初始化一个ElementWrapper对象，该对象包含一个root属性，这时root就不为null了，而是一个div。

