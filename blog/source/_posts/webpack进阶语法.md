---
title: webpack进阶语法
categories:
  - JavaScript
tags:
  - webpack
toc: false
date: 2020-03-18 14:07:08
---

webpack进阶语法

<!--more-->

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


### 多页面应用打包(MPA)

1. 更改项目文件目录
 - index
	 - index.js
	 - index.less
 - search
	 - index.js
	 - search.less
2. 利用glob.sync 动态获取 entry和html-webpack-plugin的数量
```
// 编写函数
const setMPA = () => {
  const entryFiles = glob.sync(path.join(__dirname, './src/*/index.js')) // ['src/index/index.js']

  const entry = {}
  const htmlWebpackPlugins = []
  Object.keys(entryFiles).map((keys) => {
    const entryFile = entryFiles[keys]
    const match = entryFile.match('/src\/(.*)\/index\.js')
    const pageName = match && match[1]
    entry[pageName] = entryFile

    htmlWebpackPlugins.push(
      new htmlWebpackPlugins({
        template: path.join(__dirname, `./src/${pageName}/index.js`),
        filename: `${pageName}.html`,
        inject: true,
        chunks: [pageName],
        minify: {
          html5: true,
          collapseWhitespace: true,
          preserveLineBreaks: false,
          minifyCSS: true,
          minifyJS: true,
          removeComments: false
        }

      })
    )

    return {
      entry,
      htmlWebpackPlugins
    }
  })
}

plugins: [].concat(htmlWebpackPlugins)

```

### sourcemap
通过 source map 定位到源代码。
开发环境开启，线上环境关闭，不然会暴露业务代码。

#### 关键字
- eval: 使⽤用eval包裹模块代码 
- source map: 产⽣生.map⽂文件 
- cheap: 不不包含列列信息
inline: 将.map作为DataURI嵌⼊js中，不不单独⽣生成.map⽂文件 
- module:包含loader的sourcemap

```
// webpack.prod.js

devtools: 'source-map' // 或其他类型 inline ...
```

### 体积优化策略
- Scope Hoisting
- Tree-shaking
- 公共资源分离
- 图片压缩
- 动态polyfill

### 提取公共资源

#### 分离基础库
如项目中使用react,react-dom,可以使用html-webpack- externals-plugin将 react、react-dom 基础 包通过 cdn 引⼊入，不打⼊入 bundle 中。
```
// webpack.prod.js
new htmlWebpackExternalPlugin({
      externals: [
        {
          module: 'react',
          entry: 'https://11.url.cn/now/lib/16.2.0/react.min.js',
          global: 'React'
        },
        {
          module: 'react-dom',
          entry: 'https://11.url.cn/now/lib/16.2.0/react-dom.min.js',
          global: 'ReactDOM'
        }
      ]
    })
// index.js
<script src="https://11.url.cn/now/lib/16.2.0/react.min.js"></script>
  <script src="https://11.url.cn/now/lib/16.2.0/react-dom.min.js"></script>
// 这里之所以在页面上引入cdn文件，是因为将html-externals-plugin写在了html-webpack-plugin之前，导致html-webpack-externals-plugin执行时找不到html插入对应的entry地址,所以还需手动引入。
```

#### 利用SplitChunksPlugin
webpack 4中的内置插件。
chunks 参数说明:
- async 异步引⼊入的库进⾏行行分离(默认)
- initial 同步引⼊入的库进⾏行行分离
- all 所有引⼊入的库进⾏行行分离(推荐)

##### 分离基础包
test: 匹配出需要分离的包
```
module.exports = { 
  optimization: {
    splitChunks: {
      cacheGroups: {
        commons: {
          test: /(react|react-dom)/,
          name: 'vendors', // 打出的包名
	  chunks: 'all'
        }
      }
    }
  }
}
```

##### 分离公共文件
```
module.exports = { 
  optimization: {
    splitChunks: {
      minSize: 0, 
      cacheGroups: {
        commons: {
          name: 'commons', // 打包出的文件名
          chunks: 'all' ,
          minChunks: 2, // 至少使用的次数
        }
      }
    }
  }
}
```
### tree shaking (摇数优化)
- 概念: 1个模块可能有多个⽅法，只要其中的某个方法使⽤到了，则整个文件都会被打到 bundle ⾥面去，tree shaking 就是只把⽤到的⽅法打⼊ bundle ，没⽤用到的⽅法会在 uglify 阶段被擦除掉。
- 使⽤: webpack 默认⽀支持，在 .babelrc ⾥里里设置 modules: false 即可 · production mode的情况下默认开启
- 要求: 必须是 ES6 的语法，CJS 的⽅方式不不⽀支持
- 生产环境下，默认开启 tree-shaking
#### 原理
- DCE(Dead code elimination)
代码不会被执行，不可到达
代码执行的结果不会被⽤用到
代码只会影响死变量(只写不读)
- 利用 ES6 模块的特点: 
	1. 只能作为模块顶层的语句出现
	2. import 的模块名只能是字符串串常量量
	3. import binding 是 immutable的 
	4. 代码擦除: uglify 阶段删除⽆无⽤用代码
#### 删除无用的CSS
- PurifyCss : 遍历代码，识别已经用到的css class
使用purgecss-webpack-plugin 和mini-css-extract-webpack-plugin 配合使用。

- uncess : HTML需要通过jsdom加载，所有的样式通过postCss解析，通过document.querySelector来识别在HTML文件里面不存在的选择器。

### 图片压缩
要求: 基于node库的imagemin或者tinypngAPI
使用: 配置image-webpack-loader
优点:
1. 支持定制选项
2. 可以引入更多的第三方插件，如pngquant
3. 可以处理多种图片格式

### scope hoisting
webpack 4 默认开启
构建后的代码，存在大量的闭包，会增大bundle文件，运行时创建的函数作用于变多，内存开销增大。

#### 原理
将所有模块的代码按照引用顺序放到一个函数作用域里，然后适当的重命名一些变量防止重名。

### 代码分割和动态import

#### 意义
对于大的 Web 应⽤用来讲，将所有的代码都放在⼀个⽂件中显然是不不够有效的，特别是当某些代码块是在某些特殊的时候才会被使⽤到时。webpack 有一个功能就是将你的代码库分割成 chunks(语块)，当代码运⾏到需要它们的时候再进⾏加载。

#### 适用的场景
- 抽离相同代码到⼀一个共享块
- 脚本懒加载，使得初始下载的代码更更⼩小
#### 懒加载Js的方式
- CommonJS:require.ensure
- ES6:动态 import(目前还没有原⽣生⽀支持，需要 babel 转换)

### 优化构建时命令行显示的日志
#### 统计信息 stats

常用的数据值

Preset | Alternative | Description
---- | ---- | ----
errors-only | none | 只在发生错误时输出
minimal | none | 只在发生错误或新编译时输出
none | false | 没有输出
normal | true | 标准输出
verbose | none | 全部输出

#### 借助friendly-errors-webpack-plugin插件
```
webpack.prod.js
module.exports = {
  stats: 'errors-only'
}
```

### 构建异常和中断处理
```
$ echo $? // 获取错误码 0 为成功，非0失败

```
#### 捕获并处理构建异常

```
// webpack.prod.js
module.exports = {
  function() {
      this.hooks.done.tap('done', (stats) => { 
        if (stats.compilation.errors &&
          stats.compilation.errors.length 
          && process.argv.indexOf('- -watch') == -1)
          {
            console.log('build error');
            process.exit(1); 
          }
        }) 
      }
}
```
### 分析webpack 
执行 npm run build 或npm run dev 等命令行时，实际是执行的node_modules/webpack/bin/webpack.js文件。

#### 分析webpack.js
- process.exitCode = 0; 
表示程序正常执行返回，error为null，exitCode 为0
- const runCommand = (command, args) => {...} 表示执行某个命令,如 npm install 
- const isInstalled = packageName => {...} 判断某个包是否存在。
- const CLIs = [...] webpack 中可用的cli,webpack-cli、webpack-command
- const isInstalledClis = ... 判断webpack的是够安装了以上两个cli
- if (installedClis.length === 0) {...} 根据安装的数量进行处理


webpack启动后，会找到webpack-cli文件,启动webpack-cli文件

#### 分析webpack-cli
- 引入yargs,对命令行进行定制
- 分析命令行参数,对各个参数进行转换,组成编译配置项
- 引用webpack,根据配置项进行编译和构建

#### webpack-cli/bin/cli.js分析
- const { NON_COMPILITION_ARGS} = utils/contants.js const NON_COMPLITION_CMD = ... 处理不需要编译的命令
- ...