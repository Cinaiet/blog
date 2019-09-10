---
title: Js继承
date: 2019-05-10 14:27:46
categories: JavaScript
tags: 如果当时
---

**前言**  js语言是一种面向对象的语言，所谓的封装、继承、多态等特征js也都具备，这篇文章中我们就来探讨一下js中的继承有哪些实现方式。

<!--more-->

如果有java、c++、oc等语言的基础的话会有一种观念，继承是基于与类的，需要说明的是js中的继承并不是基于类来实现的，而是基于原型的，尽管在ES6中有了class关键字，但在运行时中还是基于原型链来实现的。

----

### 原型的概念

在js中创建对象典型是通过new操作符来调用构造函数，得到的实例中会有一个[[prototype]]的私有属性，这个私有属性是一个构造函数的prototype属性的一个引用。查找实例属性时会先在自身查找，然后再去查找[[prototype]]，直到查找成功或[[prototype]]为空。这就是原型的基本原理。在ES6之前我们都是通过对构造函数和原型来达到继承的效果。以下我们来列举一下大概会有多少种继承的实践。

----

### 原型链

我们知道了对一个对象进行属性查找时如果在自身查找不到时会去原型中进行查找，如果仍然查找不到的话又会继续查找原型的原型，如此反复直至查找成功或没有更深层次的原型可供查找，基于这种原理，我们可以通过将被继承类型的实例作为子类型的原型即是一种实现继承的方法。

```
function SuperType() {
    this.flag = true;
}

SuperType.prototype.getSuperValue = function () {
    return this.flag;
};

function SubType() {
    this.subflag = false;
}

//继承了 Super
SubType.prototype = new SuperType();

SubType.prototype .getSubValue = function () {
    return this.subflag;
};

var instance1 = new SubType();
console.log(instance1.getSuperValue()); // true
console.log(instance1.getSubValue()); // false

console.log(instance1 instanceof SubType); // true
console.log(instance1 instanceof SuperType);// true
console.log(instance1 instanceof Object); // true

console.log(Object.prototype.isPrototypeOf(instance1)); // true
console.log(SuperType.prototype.isPrototypeOf(instance1));// true
console.log(SubType.prototype.isPrototypeOf(instance1)); // true

console.log(instance1.constructor); // [Function: SuperType]
```
根据上面代码可以看出通过将子类型的构造函数的prototype设置为父类型实例，实例化的子类型可以同时拥有子类型和父类型的属性，也就是说我们通过这种方式实现了继承，不过这种继承有一些缺点,原型链中有引用类型的属性时，该属性会被所有实例所共用，这就会造成如果有任何一个实例如果更改了原型中的应用类型的值就会影响到其他实例；再者就是在构造子类型实例时，无法向超类的构造函数中传递参数。

----

### 借用构造函数

js函数有一个call方法，调用时指定的第一个参数为函数的作用域对象，其后参数会作为调用函数的参数进行传递，使用构造函数模式定义的构造函数内部我们会将要定义的属性赋值给作用域对象（this），基于上面两点，我们可以在子类型的构造函数中使用父类型的call函数，把当前的this作为第一个参数传入，以此来获得父类型的属性。

```
function SuperType(name) {
    this.name = name;
    this.colors = ["red", "blue", "green"];
}
SuperType.prototype.sayName = function () {
    console.log(this.name);
};

function SubType(name, age) {

    //继承 SuperType
    SuperType.call(this, name);

    this.age = age;
}

var instance1 = new SubType("Bill", 22); 
instance1.colors.push("black");

console.log(instance1.name); // Bill
console.log(instance1.age); // 22
console.log(instance1.colors); // [ 'red', 'blue', 'green', 'black' ]

var instance2 = new SubType("Annie", 24);
console.log(instance2.name); // Annie
console.log(instance2.age); // 24
console.log(instance2.colors); // [ 'red', 'blue', 'green' ]
console.log(instance2.sayName); // undefined

var super1 = new SuperType();
console.log(super1.sayName); // [Function]
```

这种方法的优点是，父类型中的引用类型的属性,并不被所有实例所公用，不会有有互相串改数据的问题；在构造子类型的实例时,可以向超类的构造函数中传递参数。不过如果父类型中的函数是定义在父类型实例中的话（典型的构造函数模式就是这样），子类型个实例之间无法复用函数，如果父类型中的函数是定义在父类型原型上的，子类型中根本就继承不到，因为子类型中实例的[[prototype]]是指向子类型的构造函数的prototype的，在子类型实例中没有指向父类型prototype的指针，所以也就没继承到。

----

### 组合继承

组合原型链和借用构造函数两者的优点，使用原型链来继承父类型中的原型属性进行继承，使用构造函数对父类型的实例属性进行继承

```
function SuperType(name) {
    this.name = name;
    this.colors = ["red", "blue", "green"];
}

SuperType.prototype.sayName = function () {
    console.log(this.name);
};

function SubType(name, age) {

    //继承 SuperType
    SuperType.call(this, name);

    this.age = age;
}
SubType.prototype = new SuperType();
SubType.prototype.sayAge = function () {
    console.log(this.age);
};


var instance1 = new SubType("Bill", 22);
instance1.colors.push("black");

instance1.sayName(); // Bill
instance1.sayAge(); // 22
console.log(instance1.colors); // [ 'red', 'blue', 'green', 'black' ]

var instance2 = new SubType("Annie", 24);
instance2.sayName(); // Annie
instance2.sayAge(); // 24
console.log(instance2.colors); // [ 'red', 'blue', 'green' ]

console.log(instance1 instanceof Object); // true
console.log(instance1 instanceof SuperType); // true
console.log(instance1 instanceof SubType); // true

console.log(Object.prototype.isPrototypeOf(instance1)); // true
console.log(SuperType.prototype.isPrototypeOf(instance1)); // true
console.log(SubType.prototype.isPrototypeOf(instance1)); // true

```
组合了两种模式之后，避免了原型链和借用构造函数的缺陷,融合了他们的优点。成为JS中最常用的继承方式。仅有一点小不足就是调用了两次父类的构造函数,在子类的prototype上面创建了不必要的、多余的属性

----

### 原型式继承

原型式继承的原理是将一个对象直接作为新对象的原型，使用这种模式创建的对象并没有自定义的类型，只是实现了在原有对象的基础上进行了扩展。

```
function object(o) {
    function F() {}
    F.prototype = o;
    return new F();
}

var person = {
    name: "Bill",
    friends: ["Annie", "Frank"]
};

var antherPerson = object(person);
antherPerson.name = "Greg";
antherPerson.friends.push("Rob");

console.log(person.name); // Bill
console.log(person.friends); // [ 'Annie', 'Frank', 'Rob' ]

console.log(antherPerson.name); // Greg
console.log(antherPerson.friends); // [ 'Annie', 'Frank', 'Rob' ]
```
这种模式确实可以创建出基于其他对象的实例，不过在原型链中有引用类型的属性时，该属性会被所有实例所共用;还有一个问题就是在构造子类型的实例时，无法向父类型的构造函数中传递参数。

在ES5中新增了Object.create函数，该函数规范了原型式继承的，该函数接收两个参数，原型对象以及一个可选的为新对象定义额外属性的属性描述符对象（和Object.defineProperties方法第二个参数一样）。

```
var antherPerson2 = Object.create(person, {
    name: {
        value: "Frank"
    }
});
antherPerson2.friends.push("Shelby");
console.log(antherPerson2.name); // Frank
console.log(antherPerson2.friends); // [ 'Annie', 'Frank', 'Rob', 'Shelby' ]
console.log(antherPerson.friends); // [ 'Annie', 'Frank', 'Rob', 'Shelby' ]
```
总体来说，有了Object.create函数可以是我们更加便捷的使用原型式继承，不过由于引用类型的值会被所有新创建的实例所用，这种模式个人感觉使用时还是需要比较谨慎。

----

### 寄生式继承

寄生式继承是将原型式继承使用一个工厂函数将构建过程封装起来，在工程函数中可以为创建的新对象添加所需的新的属性，最后返回新对象。

```
function createAnother(original) {
    function object(o) {
        function F() {}
        F.prototype = o;
        return new F();
    }
    var clone = object(original);
    // var clone = Object.create(original);

    clone.sayName = function () {
        console.log(this.name);
    };

    return clone;
}

function Person(name, friends) {
    this.name = name;
    this.friends = friends;
}

var person = new Person("Bill", ["Annie", "Frank"]);

var anotherPerson = createAnother(person);
anotherPerson.name = "Greg";
anotherPerson.friends.push("Rob");
anotherPerson.sayName(); // Greg

var anotherPerson1 = createAnother(person);
console.log(anotherPerson.sayName == anotherPerson1.sayName); // false
```
这种模式相对于原型模式封装程度更高一点，使用这种模式创建的实例之间不能复用函数，效率会降低。

----

### 寄生组合式继承

组合继承是最常用的继承方式，不过他会调用两次父类型的构造函数，形成在实例自身和原型上分别形成一份属性，寄生组合式继承利用构造函数来继承实例属性，通过引用一份父类型引用来获得原型链。
```
function inheritPrototype(subType, superType) {
    var prototype = Object(superType.prototype);
    prototype.constructor = subType;
    subType.prototype = prototype;
}

function SuperType(name) {
    this.name = name;
    this.colors = ["red", "blue", "green"];
}

SuperType.prototype.sayName = function () {
    console.log(this.name);
};

function SubType(name, age) {

    //继承 SuperType
    SuperType.call(this, name);

    this.age = age;
}

inheritPrototype(SubType, SuperType);

SubType.prototype.sayAge = function () {
    console.log(this.age);
};

var instance1 = new SubType("Bill", 22);
instance1.colors.push("black");

instance1.sayName(); // Bill
instance1.sayAge(); // 22
console.log(instance1.colors); // [ 'red', 'blue', 'green', 'black' ]
```

寄生组合式继承避免了调用两次父类型的构造函数，避免在子类的prototype上面创建不必要、多余的属性，是ES6之前最理想的继承范式。

----

### class extends

在ES6之前继承只能通过上面介绍的方法，使用函数，原型，各种组合的手段才能实现较为理想的继承方式，在ES6中js给出了class和extends操作符。

```
class SuperType {
	constructor(name) {
		this.name = name;
		this.colors = ['red', 'blue', 'green'];
	}
	
	sayName() {
		console.log(this.name);
	}
}

class SubType extends SuperType {
	constructor(name, age) {
		// 相当于SuperType.call(this, name);
		super(name);
		this.age = age;
	}

	sayAge() {
		console.log(this.age);
	}
}

let instance1 = new SubType('Bill', 25);
instance1.sayName(); // Bill
instance1.sayAge(); // 25
```
在定义SubType时要通过extends指明要继承的父类型，在子类型定义中可是使用super关键字来指示父类型，在constructor中调用super()函数向父类型构造函数传参,这是一个和寄生组合式等价的实现方式,在书写上和语义上都比ES5中要清晰很多，所以建议在实践中应该尽量使用class的extends关键字来实现继承。

----

### 以上

根据懒人定律以及拿来主义原则，[原文出处](http://blog.zhanghuiliang.cn/2019/05/07/js%E7%BB%A7%E6%89%BF/)

