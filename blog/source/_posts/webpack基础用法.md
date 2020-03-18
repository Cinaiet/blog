title: webpack基础用法
date: 2020-03-14 22:40:51
categories: JavaScript
tags: webpack
---

webpack 的基础用法之核心概念

<!--more -->
###  entry 入口文件

#### 单入口文件
entry是一个字符串
```
module.exports = {
  entry: 'xxx.js'
}
```

#### 多入口文件
entry是一个对象
```
module.exports = {
  entry: {
    index: 'index.js',
    search: 'search.js'
  }
}
```
### output
output 用来告诉webpack如何将编译后的文件，输出到磁盘

#### 单入口文件
只需指定output 的filename及path两个参数即可

```
const path = require(''path)

module.exports = {
  entry: 'xxx.js',
  output: {
    filename: 'bundle.js',
    path: path.join(__dirname, 'dist')
  },
  mode: 'production'
}
```
#### 多入口文件
在output中没有多入口文件说法，而是通过占位符来确保输出的文件名称唯一
```
const path = require(''path)
module.exports = {
  entry: {
    index: 'index.js',
    search: 'search.js'
  },
  output: {
    filename: '[name].js',
    path: path.join(__dirname, dist)
  },
  mode: 'production'
}
```

### loaders
webpack 开箱即用只支持JS和JSON两种文件类型。需要通过loaders去支持其他文件类型并将它们转化成有效的模块，并且可以添加到依赖图中。
loader本身是一个函数，接受源文件作为参数，返回转换的结果。

#### 常见的loader
名称 | 描述
---- | ---- 
babel-loader | 转换es6、es7等新特性语法
css-loader | 支持.css文件的加载和解析
less-loader | 将less文件转换为css
ts-loader | 将TS转换为JS
file-loader | 进行图片、字体等的打包
raw-loader | 将文件以字符串的形式导入
thread-loader | 多进程打包js和css

#### loader 的用法
```
module.exports = {
   ... // entry output 等省略
  module: {
    rules: [
      {
          test: '/\.txt$/', // test 指定匹配规则
          use: 'raw-loader' // use 指定使用的loader名称
      }
    ]
  }
}
```
----
#### 例1:  解析ES6
需要使用babel-loader 去解析，babel-loader又依赖于babel,所以项目中需要使用.babelrc文件

```
// 安装解析ES6依赖
$ npm i @babel/core @babel/preset-env @babel-loader -D
```
项目中新增.babelrc文件
```
// .babelrc

{
  "presets" : [ // 一系列plugins的集合  
    "@babel/preset-env"
  ]
}

// 修改webpack.config.js 
module.exports = {
  ... // 省略entry output 
  mode: 'production',
  module: {
      rules: [
        {
            test: /\.js$/,
            use: 'babel-loader'
        }
    ]
  }
}

$ rm -rf dist  // 清理当前dist目录
$ npm run build // 打包编译查看效果
```
----
#### 例2: 解析React JSX

```
$ npm i react react-dom @bebel/preset-react -D
 // search.js
"use strict";
import React from 'react'
import ReactDom from 'react-dom'

class Search extends React.Component {
  render() {
    return <div>Search Text </div>
  }
}

ReactDom.render(
  <Search />,
document.getElementById('root')
)
$ npm run build
```
----
#### 例3 : 解析css 、less

```
// 解析 css
// 下载 css-loader 用于加载css文件，并且转换成commonJs对象
// 下载 style-loader 将样式通过style标签，插入到head中
// less-loader 将css 转换成css
$ npm i css-loader style-loader less less-loader -D
// webpack.config.js
module.exports = {
  ... // 省略entry output 
  mode: 'production',
  module: {
      rules: [
        {
            test: /\.js$/,
            use: 'babel-loader'
        },
        {
            test: /\.css$/,
            use: [ 'style-loader', 'css-loader']
        }
    ]
}


```

**需注意，loader是链式调用，执行方式是从右到左，所以在css应先css-loader解析css，然后传递给style-loader**

----


### plugins 插件
插件用于bundle文件的优化，资源管理和环境变量注入。
作用于整个构建过程。
可以理解为 loaders没办法做的事情，通过plugins来完成，如在打包前，手动删除打包目录，可用plugins来完成。

#### 常见的plugins
名称 | 描述
---- | ---- 
CommonChunkPlugin | 讲chunks相同的模块代码，提取成公共js
CleanWebpackPlugin | 清理构建目录
CopyWebpackPlugin | 将文件或文件夹拷贝到构建的输出目录
ExtractTextWebpackPlugin | 将css从bundle文件里提取成一个独立的css文件
HtmlWebpackPlugin | 创建html文件去承载输出的bundle
UglifyjsWebpackPlugin | 压缩JS
ZipWebpackPlugin | 将打包出的资源生成一个zip包

### mode 环境
mode用来指定当前当前的构建环境是 production、development、none。
webpack 4之前没有这个概念，使用mode的好处是可以使用webpack的内置函数。如设置成development，将在开发阶段启动用于开发时使用的参数配置。
#### mode函数的内置功能

选项 | 描述
---- | ---- 
development | 设置process.env.NODE_ENV 的值为 development，开启NameChunksPlugin和NameModulesPlugin.
production | 开启FlagDependenUsagePlugin , FlagIncludedChunksPlugin , ModuleConcatenationPlugin等
none | 不开启任何优化选项

### 文件监听
文件监听，是在源码发生变化时，自动重新构建出新的输出文件
#### webpack的两种监听方式
1. 启动webpack时，带上 --watch 参数 。美中不足是每次都需要手动刷新浏览器。
2. 在配置webapack.config.js 中设置watch:true
#### 文件监听的原理
- 轮询判断文件的最后编辑时间是否发生变化
- 某个文件发生了变化，并不会立刻告诉监听者，而是先缓存起来，等待一段时间(aggregateTimeout)，若其它文件也有发生变化，则会将变化的文件列表一起去构建，生成到bundle中。
```
module.export = {
  watch: true // 默认为false 不开启
  watchOptions: { // 只有开启了监听模式，watchOptions 才有意义
    ignored: /node_modules/, // 默认为空，不监听的文件或文件夹，支持正则匹配
    aggregateTimeout: 300, // 监听到变化后，会等300ms再去执行，默认为300ms
    poll: 1000, // 判断文件是否发生变化是通过不停询问系统指定文件有没有变化实现的，默认每秒询问1000次
  }
}
```
### webpack-dev-server 热更新
```
// pageage.json 
scripts: {
  "dev": "webpack-dev-server --open"
}
$ npm i webpack-dev-server -D

// webpack.config.js
const webapck = require('webpack')
module.export = {
  // ...
  plugins: [
    new webpack.HotModuleReplacementPlugin()
  ],
  devServer: { // webpack服务基础目录
    contentBase: './dist',
    hot: true
  }
}

$ npm run dev

```

#### webpack原理分析

首先了解几个概念：

- webpack Compile: 将Js编译成Bundle
- HMR Server: 将热更新的文件传输给 HMR Runtime
- Bundle Server : 提供文件在浏览器端的访问
- HMR Runtime: 会被更新到浏览器，更新文件的变化
- bundle.js ： 构建输出的文件

#### 热更新的过程
热更新有两个过程。
1.  文件的启动阶段
  将初始代码经文件系统进行编译, 使用webpack Compile进行打包，打包好的bundle.js传输给bundle Server（服务器） ,bundle Server以server的方式让浏览器访问到。
2. 代码更新阶段
  文件系统(代码)更新后，使用webpack Compile 打包，传输给HMR Server, HMR Server(服务端)将发生变化的文件告知HMR Runtime(浏览器端)，通常以JSON数据的形式进行传输，HMR Runtime 更新代码，实现不刷新更新。

### 文件指纹
打包后输出的文件名后缀。
#### 文件指纹的生成
- **Hash** 和整个项目的构建有关，只要项目文件有更改，整个构建项目的hash值就会更改。
- **Chunkhash** 和webpack打包的chunk有关，不同的entry会生成不同的Chunkhash
- **Contenthash** 根据文件内容更新hash，文件内容不变，Contenthash不变。**注：js 没有contenthash。**

#### js文件指纹设置
设置output的filename,使用chunkhash
```
// webpack.config.js
module.export = {
   // ... 
  output: '[filename][chunkhash:8].js'
}
```

#### css文件指纹设置
一般情况下，若项目中配置使用了style-loader和css-loader,会将编译好的css，使用style-loader插入到head中，没有独立的css文件，所以需要借用MiniCssExtractPlugin将style-loader 中的css提取为一个独立的文件，使用contenthash

```
// webpack.config.js
{
  // ...
   plugins: [
     new MiniCssExtractPlugin({ filename: `[name][contenthash:8].js` })
   ]
}

 ```

#### 图片文件指纹设置

设置file-loader的name使用[hash]


```
```
// webpack.config.js
module.export = {
  // ..
  module: {
    rule: [
      test: /\.(svg|png|jpeg|gif)$/,
      use: [
        loader: 'file-loader',
        options: {
          name: 'img/[name][hash:8].[ext]'
        }
      ],
    ]
  }
}

```
---- 

|占位符名称 | 含义 |
|---- | ---- |
|ext | 资源后缀名 |
|name | 文件名称 |
|path | 文件相对路径 |
|folder | 文件所在的文件夹 |
|contenthash | 文件内容的hash, 默认md5生成 |
|hash | 文件内容的hash, 默认md5生成 |
|emoji | 一个随机的指代文件内容的emoji |


---- 


### 代码压缩


#### JS代码压缩
webpack4中内置了 uglifyjs-webpack-plugin 不需要再做处理了。

#### css代码压缩
使用optimize-css-assets-webpack-plugin，同时使用 cssnano（css预处理器）

```
$ npm i optimize-css-assets-webpack-plugin cssnano -D
// webpack.prod.js
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')
modele.export = {
  // ...
  plugins: [
    new OptimizeCssAssetsWebpackPlugin({
      assetNameRegExp: /\.css$/,
      assetProcessor: require('cssnano')
   })
  ]
}
```

#### html代码压缩
使用html-webpack-plugin设置压缩参数

```
$ npm i html-webpack-plugin -D
// webpack.prod.js
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')
modele.export = {
  // ...
  plugins: [
    new HtmlWebpackPlugin({
      template: path.join(__dirname, 'search.html'), // 模板所在位置
      filename: 'search.html', // 打包后文件名
      chunks: 'search', // 使用的chunk
      inject: true, // 自动注入css,js
      minify: {
        html5: true,
        collapseWhitespace: true,
        preserveLineBreaks: false,
        minifyCSS: true,
        minifyJS: true,
        removeComments: false
      }
    })
  ]
}
```






