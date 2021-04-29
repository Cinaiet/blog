---
title: JavaScript语句及表达式
categories:
  - JavaScript
tags:
  - 花满楼
originContent: ''
toc: false
date: 2020-06-17 23:35:28
---

在 JavaScript 标准中，语句分成了两种：声明和语句。

<!-- more -->

### 语句块
可以理解为，语句块就是一对大括号。
```
{
    var x = 100;
    var y = 20;
}
```
语句块的意义和好处在于：让我们可以把多行语句视为同一行语句，这样if、for 等语句定义起来就比较简单了。
需要注意的是，语句块会产生作用域。如:
```

{
    let x = 1;
}
console.log(x); // 报错

```
这里let声明的变量，只在语句块作用域中有效，所以在语句块外部访问x变量会报错。

### 空语句
空语句就是一个独立的分号，实际上没什么大用。
```
;
```
空语句的存在仅仅是从语言设计完备性的角度考虑，允许插入多个分号而不抛出错误。

### if语句

```
if(true) {
    console.log('asd')
}
```
if 语句的作用是，在满足条件时执行它的内容语句，这个语句可以是一个语句块，这样就可以实现有条件地执行多个语句了。
if 语句还有 else 结构，用于不满足条件时执行。

```
const a = 20;
if(a < 10) {
  console.log('a < 10')
} else if(a < 20) {
  console.log('a < 20')
} else {
  console.log('a >= 10')
}
```
表示三个互斥的分支，分别在满足不同的条件时执行。

### switch 语句

```
const a  = 20;
switch(a) {
  case 10: 
    // ... 
    break;
  case 20:
    / ... 
    break;
  case 30:
    / ... 
    break;
}
```
### 循环语句
#### while 循环和 do while 循环

```
// while
let a = 100;
while(a--) {
  console.log(a)
}

// do while
let b = 101;
do {
  console.log(a)
}while(a < 100)
```
注意，这里 do while 循环无论如何至少会执行一次。

#### for循环
```
for(var i = 0; i < 10; i++) {
  // do something...
}
```

#### for in

for in 循环枚举对象的属性，这里体现了属性的 enumerable 特征。
```
let o = {a: 1, b: 2}
Object. defineProperty(o, 'c', {enumerable: false, value: 3})
for(let p in o)
    console.log(p)

```
上一段代码中，定义了一个对象o，并且给它添加了一个不可枚举属性c，之后用for in 循环枚举它的属性，输出的只有a和b。
如果我们定义 c 这个属性时，enumerable 为 true，则 for in 循环中也能枚举到它。

#### for of 循环和 for await of 循环

```
for(let e of [1, 2, 3, 4, 5])
    console.log(e)
```
它背后的机制实际上是iterator.
```
let o = {  
    [Symbol.iterator]:() => ({
        _value: 0,
        next(){
            if(this._value == 10)
                return {
                    done: true
                }
            else return {
                value: this._value++,
                done: false
            };
        }
    })
}
for(let e of o)
    console.log(e)
```
上一段代码中展示了如何为一个对象添加 iterator。但是，在实际操作中，我们一般不需要这样定义 iterator，我们可以使用 generator function。

```
function* foo(){
    yield 0;
    yield 1;
    yield 2;
    yield 3;
}
for(let e of foo())
    console.log(e)
```
此外，JavaScript 还为异步生成器函数配备了异步的 for of。

```
function sleep(duration) {
    return new Promise(function(resolve, reject) {
        setTimeout(resolve,duration);
    })
}
async function* foo(){
    i = 0;
    while(true) {
        await sleep(1000);
        yield i++;
    }  
}

for await(let e of foo())
    console.log(e)

```

上一段代码定义了一个异步生成器函数，异步生成器函数每隔一秒生成一个数字，这是一个无限的生成器。
接下来，我们使用 for await of 来访问这个异步生成器函数的结果，我们可以看到，这形成了一个每隔一秒打印一个数字的无限循环。
但是因为我们这个循环是异步的，并且有时间延迟，所以，这个无限循环的代码可以用于显示时钟等有意义的操作。

### return
return 语句用于函数中，它终止函数的执行，并且指定函数的返回值。
```
function a() {
  return 'a'
}
```
### break 语句和 continue 语句
break 语句用于跳出循环语句或者 switch 语句，continue 语句用于结束本次循环并继续循环。

### try 语句和 throw 语句
try 语句和 throw 语句用于处理异常。

```
try {
    throw new Error("error");
} catch(e) {
    console.log(e);
} finally {
    console.log("finally");
}
```
一般来说，throw 用于抛出异常，但是单纯从语言的角度，我们可以抛出任何值，也不一定是异常逻辑，但是为了保证语义清晰，不建议用 throw 表达任何非异常逻辑。

try 语句用于捕获异常，用 throw 抛出的异常，可以在 try 语句的结构中被处理掉：try 部分用于标识捕获异常的代码段，catch 部分则用于捕获异常后做一些处理，而 finally 则是用于执行后做一些必须执行的清理工作。

catch 结构会创建一个局部的作用域，并且把一个变量写入其中，需要注意，在这个作用域，不能再声明变量 e 了，否则会出错。

在 catch 中重新抛出错误的情况非常常见，在设计比较底层的函数时，常常会这样做，保证抛出的错误能被理解。

finally 语句一般用于释放资源，它一定会被执行，即使在 try 中出现了 return，finally 中的语句也一定要被执行。

### var 
var 声明语句是古典的 JavaScript 中声明变量的方式。而现在，在绝大多数情况下，let 和 const 都是更好的选择。


### let 和 const
新设计的变量声明语法。

相较于var声明的优点:
- 在重复声明变量时会报错，避免变量名冲突。
- 定义变量之前调用会报错。

### class 声明

```
class {

}

```
最基本的用法只需要 class关键字，加一对大括号。它的声明特征跟 const 和 let 类似，都是作用于块级作用域。

### 函数声明

函数声明的关键字是 function

```
function () {}

function * a() {}

async function* b() {
  await a()
}

async function* b(){
    await a(3000);
    yield 1;
}
```
带 * 的函数是 generator。生成器函数可以理解为返回一个序列的函数，它的底层是 iterator 机制。

async 函数是可以暂停执行，等待异步操作的函数，它的底层是 Promise 机制。异步生成器函数则是二者的结合。

### 表达式语句

一般来说，我们的表达式语句要么是函数调用，要么是赋值，要么是自增、自减，否则表达式计算的结果没有任何意义。
但是从语法上，并没有这样的限制，任何合法的表达式都可以当做表达式语句使用。

#### PrimaryExpression 主要表达式

任何表达式加上圆括号，都被认为是 Primary Expression

```
(a + b)
```
#### MemberExpression 成员表达式

```

a.b;
a["b"];
new.target;
super.b;

```
前面两种用法都很好理解，就是用标识符的属性访问和用字符串的属性访问。而 new.target 是个新加入的语法，用于判断函数是否是被 new 调用，super 则是构造函数中，用于访问父类的属性的语法。

#### NewExpression NEW 表达式

```
new Cls()
```

#### CallExpression 函数调用表达式

```

a.b(c);
super();

```

#### LeftHandSideExpression 左值表达式
左值表达式就是可以放到等号左边的表达式。

```
a() = b ;
a().c = b;
```

#### AssignmentExpression 赋值表达式

```
a = b;
```

#### 更新表达式 UpdateExpression

```
-- a;
++ a;
a --
a ++
```
更新表达式会改变一个左值表达式的值。分为前后自增，前后自减一共四种。

#### 一元运算表达式 UnaryExpression
它的特点就是一个更新表达式搭配了一个一元运算符。

```
delete a;
typeof a;
```

#### 乘方表达式 ExponentiationExpression
乘方表达式也是由更新表达式构成的。它使用**号。
```
++i ** 30
2 ** 30 //正确
-2 ** 30 //报错
```

#### 乘法表达式 MultiplicativeExpression

```
a * b;

// 乘法表达式有三种运算符
*
/
%
// 它们分别表示乘、除和取余。它们的优先级是一样的，所以统一放在乘法运算表达式中。

```
#### 加法表达式 AdditiveExpression
加法表达式是由乘法表达式用加号或者减号连接构成的。
```
a + b - c;
```

#### 移位表达式 ShiftExpression
移位表达式由加法表达式构成，移位是一种位运算，分成三种：
```
<< 向左移位
>> 向右移位
>>> 无符号向右移位
```
移位运算把操作数看做二进制表示的整数，然后移动特定位数。所以左移 n 位相当于乘以 2 的 n 次方，右移 n 位相当于除以 2 取整 n 次。

#### 关系表达式 RelationalExpression
移位表达式可以构成关系表达式，这里的关系表达式就是大于、小于、大于等于、小于等于等运算符号连接，统称为关系运算。

```
<=
>=
<
>
instanceof 
in
```
需要注意，这里的 <= 和 >= 关系运算，完全是针对数字的，所以 <= 并不等价于 < 或 ==。

#### 相等表达式 EqualityExpression
在语法上，相等表达式是由关系表达式用相等比较运算符（如 ==）连接构成的。所以我们可以像下面这段代码一样使用，而不需要加括号。
```
a instanceof "object" == true

```

相等表达式由四种运算符和关系表达式构成
- ===
- !==
- ==
- !=

虽然标准中写的==十分复杂，但是归根结底，类型不同的变量比较时==运算只有三条规则:
- undefined 与 null 相等；
- 字符串和 bool 都转为数字再比较；
- 对象转换成 primitive 类型再比较。
- 即使字符串与 boolean 比较，也都要转换成数字
- 对象如果转换成了 primitive 类型跟等号另一边类型恰好相同，则不需要转换成数字。

#### 位运算表达式
位运算表达式含有三种：
- 按位与表达式 BitwiseANDExpression
- 按位异或表达式 BitwiseANDExpression
- 按位或表达式 BitwiseORExpression。

按位与表达式由按位与运算符（&）连接按位异或表达式构成，按位与表达式把操作数视为二进制整数，然后把两个操作数按位做与运算。

按位异或表达式由按位异或运算符（^）连接按位与表达式构成，按位异或表达式把操作数视为二进制整数，然后把两个操作数按位做异或运算。异或两位相同时得 0，两位不同时得 1。

异或运算有个特征，那就是两次异或运算相当于取消。所以有一个异或运算的小技巧，就是用异或运算来交换两个整数的值。

```

let a = 102, b = 324;
a = a ^ b;
b = a ^ b;
a = a ^ b;
console.log(a, b)
```

按位或表达式由按位或运算符（|）连接相等表达式构成，按位或表达式把操作数视为二进制整数，然后把两个操作数按位做或运算。

#### 逻辑与表达式和逻辑或表达式

逻辑与表达式由按位或表达式经过逻辑与运算符连接构成，逻辑或表达式则由逻辑与表达式经逻辑或运算符连接构成。

这里需要注意的是，这两种表达式都不会做类型转换，所以尽管是逻辑运算，但是最终的结果可能是其它类型。

```
false || 1
1 && 2
```

#### 条件表达式 ConditionalExpression
条件表达式由逻辑或表达式和条件运算符构成，条件运算符又称三目运算符，它有三个部分，由两个运算符?和:配合使用。
```
const a = 2, b = 3;
const c = a === 2 ? a : b;
```

