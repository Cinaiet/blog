---
title: ES6常用语法总结
date: 2019-02-27 16:04:29
categories: JavaScript
tags: 平行宇宙
---

**前言**: 本文中所有api及语法糖均是基于阮一峰老师的[ES6标准入门(第三版)](http://es6.ruanyifeng.com/)做的汇总及扩展。
<!-- more -->
### let 和 const命令
#### let 命令
- 描述: 新增let命令，用于声明变量，用法类似于var。但声明的变量只在let所在的代码块内有效。
```
{
	let a = 10;
	var b = 20;
}
a // Uncaught ReferenceError: a is not defined
b // 20 
```
##### 不存在变量提升，不允许重复声明
- var 命令会发生变量提升，及变量在声明之前使用，值为undefined。let命令纠正了这种现象，let声明的变量一定要在声明之后使用，不然会报错。
```
console.log(a) // ReferenceError: a is not defined

console.log(b) // undefined 
var b = 3
```
- 不允许重复定义
```
let a = '123'
a // '123'
let a = '456' // 报错 Uncaught SyntaxError: Identifier 'a' has already been declared
```
##### 块级作用域
-  在es5中，只有函数作用域和全局作用域，全局作用域导致部分场景下的变量污染或变量覆盖。
```
var tmp = new Date();

function f() {
  console.log(tmp);
  if (false) {
    var tmp = 'hello world';
  }
}

f(); // undefined
```
- es6增加了块级作用域及允许块级作用域的任意嵌套
```
function f1() {
  let n = 5;
  if (true) {
    let n = 10;
  }
  console.log(n); // 5

{{{{
  let insane = 'Hello World';
  { 
    let insane = 'Hello Cinaiet'
    console.log(insane) // Hello Cinaiet
  }
  console.log(insane) // Hello World
}}}}
}
```
#### const 命令
- const 声明一个只读的变量，一旦声明，常量的值就不能改变。
```
const PI = 3.1415;
PI // 3.1415

PI = 3; // TypeError: Assignment to constant variable.
```
-  本质
const 实际并不是变量的值不可改，而是变量指向的内存地址所保存的数据不可更改。
##### ES6 声明变量的六种方法
- var 
- function
- let  
- const 
- import
- class

### 变量的解构析构
#### 解构
##### 允许按照一定的模式从数组或是对象中提取某个值，然后对变量进行赋值
以前，为变量赋值，只能直接指定值。
```
let a = 1;
let b = 2;
let c = 3;
```
ES6 允许写成下面这样。

```
let [a, b, c] = [1, 2, 3];
let obj = { d: 'd', e: 'e', f: 'f'}
const {
	d, e, f
} = obj
d // 'd'
```
##### 允许指定默认值，也可以解构赋值
```
let [foo = true] = [] 
foo // true
let [x, y = 'yyy'] = ['xxx']
y // yyy
x // xxx

let {foo, bar} = {foo: 'foo', bar: 'bar'}
foo // foo
```
### 字符串的扩展
#### includes(), startsWith(), endsWith()
传统上，Js只有indexOf 方法，用来确定一个字符串中是否包含在另一个字符串中。ES6又提供了三种新方法。
- includes()：返回布尔值，表示是否找到了参数字符串。
- startsWith()：返回布尔值，表示参数字符串是否在原字符串的头部。
- endsWith()：返回布尔值，表示参数字符串是否在原字符串的尾部。

```
let aaa = 'hello Cinaiet'
aaa.includes('hello') // true
aaa.startsWith('hello') // true
aaa.endsWith('Cinaiet') // true

```
#### repeat()
repeat方法返回一个新字符串，表示将原字符串重复n次。

```
'x'.repeat(3) // 'xxx'
'aaa'.repeat(0) // ''
```
#### 模板字符串
相较于传统的模板字符串，使得模板更加简洁便利
```
// 传统模板
$('#result').append(
  'There are <b>' + basket.count + '</b> ' +
  'items in your basket, ' +
  '<em>' + basket.onSale +
  '</em> are on sale!'
);

// ES6模板字符串
$('#result').append(`
  There are <b>${basket.count}</b> items
   in your basket, <em>${basket.onSale}</em>
  are on sale!
`);
```
未完待续
