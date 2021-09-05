---
title: Js构造对象的过程
categories:
  - JavaScript
tags:
  - 核心原理
originContent: ''
toc: false
date: 2021-07-12 00:01:53
---

早在JavaScript 1.0 时代，JavaScript是没有继承的。

<!--more-->

## 基于对象的 JavaScript

### 1.0 如何实现面向对象的（类抄写）

```
function Car() {

  this.name = "Car";

  this.color = "Red";

}

var x = new Car();
```

在JavaScript 1.0中，将函数作为**构造器**，并且在函数中向它的实例（也就是this对象）抄写类声明的属性。

在当时的面向对象理论里面，就已经可以称这个函数为**类**，而这个被创建出来的实例为**对象**了。

所以，有了类、对象，以及一个约定的构造过程，有了这三个东西，JavaScript 就声称了自己是一门“面向对象”的语言，并且还是一门"有类语言"。

所谓“类抄写”，就是将类所拥有的属性声明一项一项地抄写到对象上面，而这个对象，也就是我们现在大家都知道的 this 引用。

## 类与构造器

由于在这样的构造过程中，this是作为new运算所构造出来的那个实例来使用的，因此 JavaScript 1.0 约定全局环境中不能使用this的。因为全局环境与new运算无关，全局环境中也并不存在一个被new创建出来的实例。

而随着JavaScript 1.1的到来，JavaScript 支持“原型继承”了。

```
function Device() {

  this.id = 0; 

}

function Car() {

  this.name = "Car";

  this.color = "Red";
}

Car.prototype = new Device();

var x = new Car();

console.log(x.id);
```

在这个例子中所创建出来的对象x是“Car()”的一个实例，但是在面向对象编程（OOP）中，x既是Car()的子类实例，也是“Device()”的子类实例，这是 OOP 的继承性所约定的基本概念。这正是这门语言很有趣的地方：**一方面使用了类继承的基础结构和概念，另一方面又要实现原型继承和基于原型链检查的逻辑。**

```
//  `x`是`Device()`的子类实例吗？
x instanceof Device

true



// 因为

x.[[Prototype]] === Car.prototype

// 且

Car.prototype = new Device()


// 所以

x.[[Prototype]].[[Prototype]] === Device.prototype
```

## ECMAScript 6 之后的类

在 ECMAScript 6 之前，JavaScript 中的函数、类和构造器这三个概念是混用的。一般来说，它们都被统一为“函数 Car()”这个基础概念，而当它用作“x = new Car()”这样的运算，或从x.constructor这样的属性中读取时，它被理解为构造器；当它用作“x instanceof Car”这样的运算，或者讨论 OOP 的继承关系时，它被理解为类。

从 ECMAScript 6 开始，JavaScript 有了使用class来声明“类”的语法。例如：

```
class AClass {

  ...

}
```

自此之后，JavaScript 的“类”与“函数”有了明确的区别：类只能用 new 运算来创建，而不能使用“()”来做函数调用。例如：

```
class AClass {
  
}

new AClass() // AClass {}

AClass() // TypeError: Class constructor AClass cannot be invoked without 'new'
```

在 ECMAScript 6 之后，JavaScript 内部是明确区分方法与函数的：不能对方法做 new 运算。例如：

```
// 声明一个带有方法的对象字面量
obj = { foo() {} }
// { foo: [Function: foo] }

// 对方法使用new运算会导致异常
 new obj.foo()

// TypeError: obj.foo is not a constructor
```


在 ECMAScript 6 之后，函数可以简单地分为三个大类：

1. 类：只可以做 new 运算；
2. 方法：只可以做调用“( )”运算；
3. 一般函数：（除部分函数有特殊限制外，）同时可以做 new 和调用运算。

其中，典型的“方法”在内部声明时，有三个主要特征：

1. 具有一个名为“主对象[[HomeObject]]”的内部槽；
2. 没有名为“构造器[[Construct]]”的内部槽；
3. 没有名为“prototype”的属性。

## 创建this的顺序问题

如果对 ECMAScript 6 之前的构造器函数（例如f）使用new运算，那么这个 new 运算会使用f.prototype作为原型来创建一个this对象，然后才是调用f()函数，并将这个函数的执行过程理解为“类抄写（向用户实例抄写类所声明的属性）”。从用户代码的视角上来看，这个新对象就是由当前new运算所操作的那个函数f()创建的。

这在语义上非常简洁明了：由于f()是 this 的类，因此f.prototype决定了 this 的原型，而f()执行过程决定了初始化 this 实例的方式。但是它带了一个问题：从 JavaScript 1.1 开始至今都困扰 JavaScript 程序员的问题：

**<span>无法创建一个有特殊性质的对象，也无法声明一个具有这类特殊性质的类。</span>**

这是什么意思呢？比如说，所有的函数有一个公共的父类 / 祖先类，称为Function()。所以你可以用new Function()来创建一个普通函数，这个普通函数也是可以调用的，例如：

```
> f = new Function;


> f instanceof Function

true


> f()

undefine
```

你也确实可以用传统方法写一个Function()的子类，但这样的子类创建的实例就不能调用。例如：

```
> MyFunction = function() {};


> MyFunction.prototype = new Function;


> f = new MyFunction;


> [f instanceof MyFunction, f instanceof Functcion]

[ true, true ]


> f()

TypeError: f is not a func
```

至于原因: JavaScript 所谓的函数，其实是“一个有[[Call]]内部槽的对象”。而Function()作为 JavaScript 原生的函数构造器，它能够在创建的对象（例如this）中添加这个内部槽，而当使用上面的继承逻辑时，用户代码（例如MyFunction()）就只是创建了一个普通的对象，因为用户代码没有能力操作 JavaScript 引擎层面才支持的那些“内部槽”。

所以到了ECMAScript 6，它的“类声明”采用了不同的构造逻辑。ECMAScript 6 要求所有子类的构造过程都不得创建这个this实例，并主动的把这个创建的权力“交还”给父类、乃至祖先类。这也就是 ECMAScript 6 中类的两个著名特性的由来，即，如果类声明中通过 extends 指定了父类，那么：

必须在构造器方法（constructor）中显式地使用super()来调用父类的构造过程；

在上述调用结束之前，是不能使用this引用的。

**ECMAScript 6 的类是由父类或祖先类创建this实例的。**

如果类声明class中不带有extends子句，因为它无法找到一个显式指示的父类，那么它所创建出来的类与传统 JavaScript 的函数 / 构造器是一样的，也就是由自己来创建this对象。

**文章出自 [周爱民老师的JavaScript核心原理解析](https://time.geekbang.org/column/intro/100039701) 侵权请联系删除。**


