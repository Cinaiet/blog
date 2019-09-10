---
title: Js创建对象
date: 2019-05-10 11:41:02
categories: JavaScript
tags: 如果当时
---

**前言**  五一假期和[阿亮](http://blog.zhanghuiliang.cn)约耍，闲聊时说起了Js创建对象的几种方式，顿觉虎躯一震，是时候抽个时间整理下这个东西了。但对于我这种懒人来说，随便一个理由都能是我没时间写东西的借口。还好，有阿亮这个勤劳的人儿。[原文出处](http://blog.zhanghuiliang.cn/2019/05/05/js%E5%AF%B9%E8%B1%A1%E5%88%9B%E5%BB%BA/)


<!--more-->

----


### 字面量

创建一个对象最简单的方法莫过于使用字面量：

```
var animalA = {
	name: 'animal',
	age: 1,
	relations: [],
	run: function () {
		console.log('run');
	}
};
```
这里我们创建了一个animalA对象，他具有三个属性，一个方法。这种创建对象的方法因为简单，所以也是较为常用的一种方法，不过一次只能创建出一个对象，也就是这里的animalA，如果要再次创建一个同样结构的animalB就需要重复写一遍后面的字面量，创建过程不能重用。

----

### 工厂模式

工厂模式是使用一个函数来封装对象的创建过程，每次创建对象是就通过对工厂方法的调用来实现。

```
function createAnimal(name, age) {
	var animal = {};
	animal.name = name;
	animal.age = age;
	animal.relations = [];
	animal.run = function () {
		console.log('run');
	};
	return animal;
}

var animalA = createAnimal('animalA', 1);
var animalB = createAnimal('animalB', 2);

console.log(animalA.constructor, animalB.constructor); // Object() {}, Object() {}
```
这里我们定义了一个createAnimal函数，每次调用即可返回一个具有3个属性，一个方法的对象。工厂模式规避了字面量方式创建相似对象时比较繁琐的问题，但是他没有产生对象类型标示，通过打印animalA和animalB的constructor可以发现输出的都是Object。


----

### 构造函数模式

在js中，可以通过函数名前添加new操作符来创建对象。

```
function Animal (name, age) {
	this.name = name;
	this.age = age;
	this.relations = [];
	this.run = function () {
		console.log('run');
	};
}

var animalA = new Animal('animalA', 1);
var animalB = new Animal('animalB', 2);

console.log(animalA.constructor); // Animal() {}
console.log(animalA.run === animalB.run); // false
```
使用new操作符调用函数时，运行环境会创建一个新对象，然后将构造函数的作用域赋给新对象（this也就指向这个新对象），运行函数，在函数运行结束后如果没有显式的return值，就会将创建的这个新对象作为返回值返回。

使用构造函数可以重用创建过程，创建的对象具有相同的类型标识（constructor），不过我们对比初始化出的两个实例的run方法会发现它们并不相等，也就是说每次每个实例对象都独立拥有一个自己的run方法，这显然是不够经济且不符合我们对它们的一致性的期望。

----

### 原型模式

js函数具有一个prototype属性，使用函数名前加new操作符创建的实例内部会有一个[[prototype]]指针指向构造函数的prototype属性,访问实例的属性时会先对实例自身进行查找，如果查找不到会继续查找实例的[[prototype]]，查找仍然失败就继续查找[[prototype]]的[[prototype]]，依此规则持续进行直到查找成功或失败。所以，将属性放在函数的prototype属性上可以实现实例之间共享数据的效果。

```
function Animal() {}
Animal.prototype.name = '';
Animal.prototype.age = 0;
Animal.prototype.relations = [];
Animal.prototype.run = function() {
	console.log("run");
};

var animalA = new Animal();
var animalB = new Animal();

console.log(animalA instanceof Animal); // true
console.log(animalA.run === animalB.run); // true
console.log(animalA.relations); // []
animalB.relations.push('fish');
console.log(animalA.relations); // ['fish']
```
使用原型模式由于所创建的实例的[[prototype]]都指向的是Animal.prototype，所以可以看到他避免了构造函数模式两次创建的实例的run函数不相等的问题，但是原型上的其他引用类型的属性也被共用，因此导致了一个实例更改了原型中引用类型的数据，其他实例访问数据也会变化。

----

### 组合构造函数和原型模式

仅使用构造函数实例间不能重用函数，仅使用原型模式重用的函数的同时也会导致引用类型的值的重用，因此不难想象我们可以组合使用两者。
```
function Animal(name, age) {
	this.name = name;
	this.age = age;
	this.relations = [];
}
Animal.prototype.run = function () {
	console.log('run');
};

var animalA = new Animal('dog', 1);
var animalB = new Animal('cat', 1);

console.log(animalA.run === animalB.run);// true
console.log(animalA.relations); // []
animalB.relations.push('fish');
console.log(animalA.relations); // []
```
组合使用了构造函数模式和原型模式后，解决了对象类型标示问题，可以重用constructor和其他函数,这是一个典型的对象创建的范例。

----

### 动态原型模式

构造函数模式和原型模式组合使用后确实是一种不错的实践方式，不过在为构造函数的原型添加函数的代码在构造函数之外，这自然是给人一种封装不足的感觉，我们可以通过在构造函数内部,对函数原型是否已经赋值的判断来动态的添加原型方法,从而实现进一步的封装。

```
function Animal(name, age) {
	this.name = name;
	this.age = age;
	this.relations = [];
	if (!Animal.prototype.run) {
		Animal.prototype.run = function () {
			console.log('run');
		};
	}
}

var animalA = new Animal('dog', 1);
var animalB = new Animal('cat', 1);

console.log(animalA.constructor); // Animal() {}
console.log(animalA.run === animalB.run);// true
console.log(animalA.relations); // []
animalB.relations.push('fish');
console.log(animalA.relations); // []
```
通过代码可以看到，我们将在原型上添加函数的代码移至函数内部，通过最初的一次判断来确定为原型添加函数的逻辑只会在构造函数第一次被调用时执行。动态原型模式可以理解为是组合构造函数和原型模式的另一种写法。

----

### 寄生构造函数

```
function Animal(name, age) {
	var animal = {};
	animal.name = name;
	animal.age = age;
	animal.relations = [];
	animal.run = function () {
		console.log('run');
	};
	return animal;
}
var animalA = new Animal('dog', 1);
var animalB = new Animal('cat', 1);

console.log(animalA.constructor); // Object() {}
console.log(animalA instanceof Animal); // false
```
寄生构造函数除了使用new操作符调用和把函数叫做构造函数外，看起来跟工厂模式没有任何区别。寄生构造函数模式创建的实例与构造函数的原型属性之间没有关系，所以实例的constructor并不指向构造函数，也无法使用instanceof来判断实例的类型。正常情况下不推荐使用，除非你要为已有类型扩展功能（装潢模式）,比如:
```
function SpecialArray() {
	var array = new Array();
	array.push.apply(array, arguments);
	array.toPipedString = function () {
		return this.join("|");
	};
	return array;
}

var colors = new SpecialArray('red', 'blue', 'grey');
console.log(colors.toPipedString()); // red|blue|grey
```

----

### 稳妥构造函数模式

Douglas Crockford发明了JavaScript中的稳妥对象的概念，指的是没有公共属性，而且其方法也不引用this的对象。稳妥对象适用于一些安全的环境中（禁止使用new和this），稳妥构造函数和寄生构造函数类似，但有亮点不同，一是新创建对象的实例方法中不使用this，二是不使用new操作符调用构造函数。
```
function Animal(name, age) {
	var animal = {};
	animal.run = function () {
		console.log(name + ' run');
	};
	return animal;
}
var animalA = new Animal('dog', 1);
animalA.run();
```

----

### class
ES6中新添加了class关键字，可以通过class来定义类，通过对类进行实例化来创建对象

```
class Animal {
	constructor(name, age) {
		this.name = name;
		this.age = age;
	}

	run() {
		console.log('run');
	}

	static hello() {
		console.log('hello');
	}
}
console.log(typeof Animal); // function
var animalA = new Animal('dog', 1);
console.log(typeof animalA); // object
console.log(animalA.constructor); // class Animal1{}
console.log(animalA instanceof Animal); // true
```
使用class关键字后面跟类名，跟大括号，在大括号中名为constructor的函数就是构造函数，实例属性可以在constructor中进行初始化。
