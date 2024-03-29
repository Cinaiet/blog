---
title: 'export default function() {} '
categories:
  - JavaScript
tags:
  - 核心原理
originContent: ''
toc: false
date: 2020-06-14 18:48:37
---

无法导出一个匿名函数表达式

<!-- more -->

export 是在ES6中出现的一个模块技术。由于当前使用较多的是 Node.js 环境带有自己内置的模块加载技术，所以ES6 模块需要通过特定的命令行参数才能开启，因此它的应用一直以来也就不够广泛。

导致这种现象的根本原因在于 ECMAScript 6 模块是静态装配的，而传统的 Node.js 模块却是动态加载的。因而两种模块的实现效果与处理逻辑都大相径庭，Node.js 无法在短期内提供有效的手段帮助开发者将既有代码迁移到新的模块规范下。


### 声明语句

严格意义上讲，JavaScript 只有变量和常量两种标识符，六条声明语句中：
- let x = ...
- const x = ...
- var x = ...
- class x = ...
- function x= ...
- import x = ...


除了这六个语句之外，还有两个语句有潜在的声明标识符的能力，不过它们并不是严格意义上的声明语句（声明只是它们的语法效果）：

- try ... catch(err ...)
- for (var i = ...)


### 导出的内容

在本质上，export只能导出上述声明的六条声明语法声明的标识符，并且在导出时将他们统一称为"名字"

在语言设计中，所谓“标识符”与“名字”是有语义差别的，export 将之称为名字，就意味着这是一个标识符的子集。类似的其它子集也是存在的，例如“保留字是标识符名，但不能用作标识符。

在 JavaScript 语言的设计上，除了那些预设的标点符号（例如大括号、运算符之类），以及部分的保留字和关键字之外，事实上用户代码可以书写的只有三种东西。
1. 标识符：（通常是）一个名字
2. 字面量： 表明由它的字面含义所决定的一个值
3. 模板：一个可计算结果的字符串值

所以，如果在这个层面上解构一份你所书写的 JavaScript 代码，那么你所能书写 / 声明的，就一定只有“名字和值”。

### 解析 export

```

// 导出“（声明的）名字”
export <let/const/var> x ...;
export function x() ...
export class x ...
export {x, y, z, ...};


// 导出“（重命名的）名字”
export { x as y, ...};
export { x as default, ... };


// 导出“（其它模块的）名字”
export ... from ...;


// 导出“值”
export default <expression
```
关于导出声明的、重命名的和其它模块的名字这三种情况，其实都比较容易理解，就是形成一个名字表，让外部模块能够查看就可以了。

但是对于最后这种形式，也就是“（导出）值”的形式，事实上是非常特殊的。要导出一个模块的全部内容就必须导出“（全部的）名字和值”，然而纯粹的值没有名字，于是也就没法访问了，所以这就与“导出点什么东西”的概念矛盾了。

> export default 

### 导出语句的处理逻辑

#### export 如何导出名字?
如果只是导出一个名字，那么它其实在“某个名字表”中做一个登记项就可以了。并且 JavaScript 中也的确是这样处理的。但是实际到使用的时候，这个名字还是要绑定一个具体的值才是可以使用的。因此，一个 export 也必须理解为这样两个步骤：
1.导出一个名字
2. 为上述名字绑定一个值

```
export var x = 100;
```

1. （与 export 类似）按照语法在当前模块中声明名字，例如上面的x；
2. 添加一个当前模块对目标模块的依赖项。


有了上述的第二步操作，JavaScript 就可以依据所有它能在静态文本中发现的import语句来形成模块依赖树，最后就可以找到这个模块依赖树最顶端的根模块，并尝试加载之。

所以关键的是，在“模块 export/import”语法中 ，JavaScript 是依赖 import 来形成依赖树的，与 export 无关。但是直到目前为止（我的意思是直到找到所有导入和导出的名字，并完成所有模块的装配的现在为止），没有任何一行用户的 JavaScript 代码是被执行过的。

####  JavaScript 的装配过程

- 找到并遍历模块依赖树的所有模块（这个树是排序的），然后
- 执行这些模块最顶层的代码

所谓模块的装配过程，就是执行一次顶层代码而已。



```

export default function() {}


```

它并不是导出了一个匿名函数表达式，而是导出了一个匿名函数定义（Anonymous  Function  Definition）。

因此，该匿名函数初始化时才会绑定给它左侧的名字“default”，这会导致import f from ...之后访问f.name值会得到“default”这个名字。

```
var obj = {
  "default": function() {}
};
console.log(obj.default.name); // "default"
```

### 知识补充

1. export ...语句通常是按它的词法声明来创建的标识符的，例如export var x = ...就意味着在当前模块环境中创建的是一个变量，并可以修改等等。但是当它被导入时，在import语句所在的模块中却是一个常量，因此总是不可写的。
2. 由于export default ...没有显式地约定名字“default（或default）”应该按let/const/var的哪一种来创建，因此 JavaScript 缺省将它创建成一个普通的变量（var），但即使是在当前模块环境中，它事实上也是不可写的，因为你无法访问一个命名为“default”的变量——它不是一个合法的标识符。
3. 所谓匿名函数，仅仅是当它直接作为操作数（而不是具有上述“匿名函数定义”的语法结构）时，才是真正匿名的，例如
```
console.log((function(){}).name);  // ""

```
4. 由于类表达式（包括匿名类表达式）在本质上就是函数，因此它作为 default 导出时的性质与上面所讨论的是一致的。
5. 导出项（的名字）总是作为词法声明被声明在当前模块作用域中的，这意味着它不可删除，且不可重复导出。亦即是说即使是用var x...来声明，这个x也是在 _lexicalNames_ 中，而不是在 _varNames_ 中。
6. 没有模块会导出（传统意义上的）main()，因为 ECMAScript 为了维护模块的静态语义，而把执行过程及其入口的定义丢回给了引擎或宿主本身。

### 扩展
为什么在 import 语句中会出现“变量提升”的效果？


答： ESModule 根据 import 构建依赖树，所以在代码运行前名字就是已经存在于上下文，然后在运行模块最顶层代码，给名字绑定值，就出现了‘变量提升’的效果。
在代码真正被执行前，会先进行模块的装配过程，也就是执行一次顶层代码。所以如果import了一个模块，就会先执行模块内部的顶层代码，看起来的现象就是“变量提升”了。













