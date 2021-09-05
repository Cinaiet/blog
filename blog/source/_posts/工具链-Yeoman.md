---
title: 工具链-Yeoman
categories:
  - JavaScript
tags:
  - 花满楼
originContent: ''
toc: false
date: 2021-06-15 01:15:27
---

Yeoman是一个通用的脚手架系统，允许创建任何类型的应用程序。它允许快速开始新项目，并简化现有项目的维护。

<!--more-->

## 安装

```
$ npm install -g yo // 权限不够的话需要加上sodo
```

## 编写生成器

### 初始化项目

```
$ mkdir generator-toolchain && cd generator-toolchain
$ npm install yeoman-generator
```

**注意: package.json 的名字需是generator-开头，否则Yeoman无法识别运行的**

```
var Generator = require('yeoman-generator');

module.exports = class extends Generator {
  // The name `constructor` is important here
  constructor(args, opts) {
    // Calling the super constructor is important so our generator is correctly set up
    super(args, opts);
  }

  async method1() {
    // const answers = await this.prompt([
    //   {
    //     type: "input",
    //     name: "name",
    //     message: "Your project name",
    //     default: this.appname // Default to current folder name
    //   },
    //   {
    //     type: "confirm",
    //     name: "cool",
    //     message: "Would you like to enable the Cool feature?"
    //   }
    // ])
    // this.log("app name", answers.name);
    // this.log("cool feature", answers.cool);

    await this.fs.copyTpl(
      this.templatePath('t.html'),
      this.destinationPath('public/index.html'),
      { title: 'Templating with Yeoman' }
    );

  }

};
```

- Yeoman 会顺次的执行class里面的方法
- npm link 会将本地的模块link到npm的标准模块中去

### Vue 脚手架生成器



