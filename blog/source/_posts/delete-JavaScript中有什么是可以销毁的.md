---
title: 'delete, JavaScript中有什么是可以销毁的'
categories:
  - JavaScript
tags:
  - 核心原理
toc: false
date: 2020-06-11 22:54:47
---

在了解delete之前，先说下JavaScript的数据类型的分类。
<!-- more -->
### 数据类型
#### 值类型
所谓值类型中的字符串是按照引用来赋值和传递引用（而不是传递值）的。
#### 引用类型
JavaScript中强行定义了Object和Function 就是引用类型。
及对象和函数，按照引用来传递和使用。

## delete

绝大多数情况下，按数据的传递和使用来解释起来是行得通的。但是到了 delete 运算这里，就不行。

按照引用定义，delete 0 就是删除一个值，而delete x 就是删除一个值或者删除一个引用。

delete 运算的表面意思，是该运算试图销毁某种东西。而delete 0 中的 0 是一个具体的字面量的值，字面量的值是不能销毁的，所以JavaScript认为“所有删除值的 delete 就直接返回 true”


*delete 这个操作的正式语法设计并不是“删除某个东西”，而是“删除一个表达式的结果”*


所以，现在这里的 0，其实不是值（Value）类型的数据，而是一个表达式运算的结果（Result）。而在进一步的删除操作之前，JavaScript 需要检测这个 Result 的类型：
- 如果它是值，则按照传统的 JavaScript 的约定返回 true；
- 如果它是一个引用，那么对该引用进行分析，以决定如何操作。


这个检测过程说明，ECMAScript 约定：任何表达式计算的结果（Result）要么是一个值，要么是一个引用。


### GetValue

GetValue()是从一个引用中取出值来的行为。

```
x  = x
```
> 所有赋值操作的含义，是将右边的“值”，赋给左边用于包含该值的“引用”。
那么上面的x = x 可翻译为 
```
x = GetValue(x)
```

所以x = x的语义并不是“x 赋给 x”，而是“把值 x 赋给引用 x”。

所以，“delete x”归根到底，是在删除一个表达式的、引用类型的结果（Result），而不是在删除 x 表达式，或者这个删除表达式的值（Value）。

## 总结
- delete 运算符尝试删除值数据时，会返回 true，用于表示没有错误（Error）。
- delete 0 的本质是删除一个表达式的值（Result）。
- delete x 与上述的区别只在于 Result 是一个引用（Reference）。
- delete 其实只能删除一种引用，即对象的成员（Property）

## 扩充
### delete undefined
```
delete undefined // false
```
在早起的JavaScript中，undefined是一个特殊的值，是在运行期间，通过void运算，或者不返回值的函数又或者一个声明了但未赋值的变量，等等类似这样的情况来“计算得到”的。所以在JavaScript早期的版本中，是无法判断x === undefined 这样的代码的，只能判断 typeof  x === undefined 这样的代码。
后来，将undefined 声明为一个全局属性。

所以由于undefined是全局属性，所以`delete undefined`其实就是`delete global.undefined`，是删除引用，而不是删除值。而这个属性是只读的，所以就返回false了。



### ReferenceError
访问不存在的变量时，会报 
```
$ x
Uncaught ReferenceError: x is not defined

```
之所以报错是因为对x的表达式的result进行了getValue()操作。而 typeof x 不报错，是因为没有求值。