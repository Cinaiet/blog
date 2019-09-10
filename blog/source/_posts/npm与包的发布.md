---
title: npm与包的发布
date: 2019-08-28 22:51:57
categories: JavaScript
tags: 花满楼
---

**前言**   npm包的出现，一定程度上的解决了变量依赖、依赖关系等代码组织性问题。
<!--more-->

----
### 包的结构
 **包实际上是一个存档文件，即一个目录直接打包为.zip或tar.gz格式的文件，安装后解压还原为目录。**
#### package.json  包描述文件
package.json中定义了如下必需字段(未全列入)
- **name 包名** 包名必须是唯一的，规范定义它需要由小写的字母和数字组成，可以包含、. _和 -，但不允许出现空格。
- **description** 包简介。
- **version** 版本号
- **keywords** 关键字。用来NPM中的分类搜索。
- *未完待续...*
----
### 发布包
为了将整个流程串联起来，这里演示如何编写一个包，将其发布到npm仓库中，并通过npm安装到本地。

#### 编写模块

```
// 编写hello.js
exports.sayHello = function () {
  return 'Hello World'
}
```
#### 初始化包描述文件
package.json 文件的内容尽管相对较多，但实际发布一个包时不需要一行行编写。NPM提供 **npm init** 命令来帮助生成package.json 文件。
```
$ npm init 

// 以下内容NPM通过提问式的交互形式，来逐个填写。
{
  "name": "wgw_test",
  "version": "0.0.1",
  "description": "A Wgw Hello Test",
  "main": "hello.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/Cinaiet/wgw_hello.git"
  },
  "keywords": [
    "wgw",
    "hello",
    "world"
  ],
  "author": "cinaiet",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/Cinaiet/wgw_hello/issues"
  },
  "homepage": "https://github.com/Cinaiet/wgw_hello#readme"
}
```

#### 注册npm包仓库账号
为了维护包，NPM必须要使用仓库账号才允许将包发布到仓库中。注册命令是**npm adduser**，这也是一个提问式的交互过程。
*注：注册成功之后，务必去npm官网激活邮箱， 这点很重要，关联到包能否正常发布*

```
$ npm adduser
  Username: xxx
  Password: xxx
  Email: xxx
```

#### 上传包
上传包的命令是**npm publish <folder>**。在刚刚创建package.json的文件夹下，执行**npm publish .**，开始上传包。


#### 安装包
安装包的命令是**npm install xxx**或 **npm install xxx -g  **(全局安装)

----
以上。

