---
title: 单元测试工具-Mocha
categories:
  - JavaScript
tags:
  - 大千世界
toc: false
date: 2021-06-20 15:15:25
---

项目中如果有组件/库的使用频率是比较高的，那么编写测试用例的收益也是非常高的。

<!--more-->

## 测试工具 Mocha

Mocha是一个功能丰富的JavaScript测试框架，运行在Node.js和浏览器上，使异步测试变得简单和有趣。

```
$ npm install mocha -D
```

Mocha 最早是一个针对Node.js的测试框架，所以调用方式上默认使用webpack/babel之前，他都不支持export import的。

```
var assert = require('assert'); // 
describe('Array', function() { // describe 分组/分目录
  describe('#indexOf()', function() {
    it('should return -1 when the value is not present', function() {
      assert.equal([1, 2, 3].indexOf(4), -1);
    });
  });
});
```

assert 中包含一些断言，如equal 相等的断言。

it 中编写一些单元测试用例。

### 编写一个加法测试用例

```
// index.js
function add(a, b) {
  return a + b;
}
module.exports = add

test/test.js
var assert = require('assert'); // assert 中有一些断言，如equal 相等的断言
var add = require('./../index.js');describe("add func testing", function() {
  it(' 1 + 2 should be 3', function() {
    assert.equal(add(1, 2), 3);
  });

  it("-5 + 2 should be -3", function() {
    assert.equal(add(-5, 2), -3);
  })
})
```

### 支持 export import 形式的引入

```
$ npm i @babel/core @babel/register -D
```

node 后续运行时所需要 require 进来的扩展名为 `.es6`、`.es`、`.jsx`、 `.mjs` 和 `.js` 的文件将由 Babel 自动转换。

#### 添加.babelrc



```
{
  "presets": ["@babel/preset-env"],
}
```

#### 改写为import export形式

```
// index.js
export function add(a, b) {
  return a + b;
}

// test/test.js
var assert = require('assert'); // assert 中有一些断言，如equal 相等的断言
import {add, mul} from './../index.js'
describe("add func testing", function() {
  it(' 1 + 2 should be 3', function() {
    assert.equal(add(1, 2), 3);
  });
  
  it("-5 + 2 should be -3", function() {
    assert.equal(add(-5, 2), -3);
  })
})
```

#### run

```
$ node_modules/.bin/mocha --require @babel/register
```

### code coverage

在实际的测试用例编写中，我们可能无法清晰的知道用例是否已经全部覆盖了编写的程序，所以需要code coverage来检查程序覆盖率。

#### nyc

```
$ npm i nyc -D
```

nyc 和bable之间相互是无法识别的，需要安装相互间的插件

### 中间件

```
$ npm i @istanbuljs/nyc-config-babel babel-plugin-istanbul -D
```

#### 添加.nycrc文件

```
{
  "extends": "@istanbuljs/nyc-config-babel"
}
```

#### 修改.babelrc

```
{
  "presets": ["@babel/preset-env"],
  "plugins": ["istanbul"]
}
```

#### run

```
$ nyc mocha
```





