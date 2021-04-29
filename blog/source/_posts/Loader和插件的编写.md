---
title: Loader和插件的编写
categories:
  - JavaScript
tags:
  - webpack
toc: false
date: 2021-01-17 17:09:00
---

webpack loader 和插件编写。
<!--more-->

## Loader
loader本身是一个函数，接受源文件作为参数，返回转换的结果。

### 执行顺序
loader的执行顺序为从右向左。


```

// webpack 采用的执行顺序(函数组合情况)
compose = (f, g) => (...args) => f(g(...args))

```
### loader-runner
**定义：** 允许在不安装webpack-cli 的前提下运行loader

**作用：** 
1.  提供独立的运行环境
2.  作为webpack的依赖，在webpack中使用它执行loader；
3.  进行loader的开发和调试。
4. [github地址](https://github.com/webpack/loader-runner)

#### 编写一个调试实例（处理es6安全性问题）

*编码为2028的字符为行分隔符，会被浏览器理解为换行，而在Javascript的字符串表达式中是不允许换行的，从而导致错误。2029为段分隔号，同2028*

- 目录结构

```
-- pageage.json
-- src
	-- demo.txt
	-- raw-loader.js
-- run-loader.js

$ npm i loader-runner -S

```

- src/demo.js



```
asd
```
- src/raw-loader.js

```
module.exports = function(source) {
  const json = JSON.stringify(source)
    .replace(/\u2028/g, '\\u2028')
    .replace(/\u2029/g, '\\u2029')

  rerurn `export defalut ${json}`
}
```

- run-loader.js

```
// 核心函数runLoaders

const path = require('path')
const fs = require('fs')
const { runLoaders } = require('loader-runner')

runLoaders({
  resource: path.join(__dirname, './src/demo.txt'), // 资源绝对路径
  loaders: [
    path.join(__dirname, './src/raw-loader.js')
  ],
  context: { // 额外上下文
    minimize: true, // 压缩参数
  },
  readResource: fs.readFile.bind(fs)

}, (err, result) => {
  err ? console.error(err) : console.log(result)
})


// 打印结果

{ result: [ 'export defalut ""' ],
  resourceBuffer: <Buffer >,
  cacheable: true,
  fileDependencies:
   [ '/Users/wangguanwei/Desktop/test/webpack/raw-loader/src/demo.txt' ],
  contextDependencies: [],
  missingDependencies: [] }
```

### 编写一个构建资源为zip包的插件
[代码仓库地址](https://github.com/Cinaiet/zip-loader)