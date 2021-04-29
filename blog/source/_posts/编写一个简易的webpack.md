---
title: 编写一个简易的webpack
categories:
  - JavaScript
tags:
  - webpack
toc: false
date: 2021-01-19 17:13:13
---

simplepack


<!--more-->

### 编写前储备知识

#### AST(abstract syntax tree)

抽象语法树，或者叫语法树。是源代码的抽象语法结构的树状变现形式，这里特指编程语言的源代码。树上的每个节点都表示源代码中的一种结构。

##### 应用场景

- 模板引擎
  常见的模板引擎的实现方式有两种
  
  1. 正则匹配
  2. 使用AST将模板引擎转换为字符串流、分析、render
- ES6 转ES5
- TS转JS

[在线Demo](https://esprima.org/demo/parse.html#)

#### webpack的模块机制

*webpack打包之后的源码此处不再po*

---

- webpack打包之后函数是一个匿名闭包(立即执行)函数.
- 匿名函数中接收一个参数，该参数是一个数组，数组中的每一项为模块初始化函数
- __webpack_require 用于加载模块，返回module.export
- 通过WEBPACK_REQUIRE_METHOD(0)启动程序

### 技术点分析

#### ES6转换为ES5

- 使用 babylon 生成AST
- 通过babel-core 将AST重新生成ES5代码

#### 分析模块之间的依赖关系

- 通过babel-traverse 的importDeclaration 方法获取依赖属性

#### 生成的文件能在浏览器中运行

### 程序实现

[代码地址](https://github.com/Cinaiet/simplepack)

