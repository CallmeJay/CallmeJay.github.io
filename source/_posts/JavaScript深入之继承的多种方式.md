---
title: JavaScript深入之继承的多种方式
date: 2018-02-26 20:55:14
categories: JavaScript
tags: 继承
---

### 原型链继承

```js
function Parent() {
  this.name = "Jay";
}

Parent.prototype.getName = function() {
  console.log(this.name);
};

function Child() {}

Child.prototype = new Parent();

var child1 = new Child();

console.log(child1.getName()); // Jay
```

<!--more-->

存在的问题：
1，引用类型的属性被所有的实例共享，eg:

```js
function Parent() {
  this.names = ["Jhon", "Michel"];
}

function Child() {}

Child.prototype = new Parent();

var child1 = new Child();

child1.names.push("Lili");

console.log(child1.names); // ["Jhon","Michel","Lili"]

var child2 = new Child();

console.log(child2.names); // ["Jhon","Michel","Lili"]
```

2，在创建 Child 的实例时，不能向 Parent 传参

### 借用构造函数（经典继承）

```js
function Parent() {
  this.names = ["Jhon", "Michel"];
}

function Child() {
  Parent.call(this);
}

var child1 = new Child();

child1.names.push("Lili");

console.log(child1.names); // ["Jhon","Michel","Lili"]

var child2 = new Child();

console.log(child2.names); // ["Jhon","Michel"]
```

优点：
1，避免了引用类型的属性被所有实例共享。
2，可以在 Child 中间向 Parent 传参. eg:

```js
function Parent(name) {
  this.name = name;
}

function Child(name) {
  Parent.call(this, name);
}

var child1 = new Child("kaer");

console.log(child1.name); // kaer

var child2 = new Child("james");

console.log(child2.name); // james
```

缺点：
方法都在构造函数里定义，每次创建实例都会创建一遍方法。

### 组合继承

原型链继承和经典继承的组合。

```js
function Parent(name) {
  this.name = name;
  this.colors = ["red", "cyan"];
}

Parent.prototype.getName = function() {
  console.log(this.name);
};

function Child(name, age) {
  Parent.call(this, name);
  this.age = age;
}

Child.prototype = new Parent();

var child1 = new Child("xiaoming", 20);

child1.colors.push("black");

console.log(child1.name, child1.age, child1.colors); // xiaoming 20 ["red", "cyan", "black"]

var child2 = new Child("david", 23);

console.log(child2.name, child2.age, child2.colors); // david 23 ["red", "cyan"]
```

优点：融合原型链继承和构造函数的优点，是 JavaScript 中最常用的继承模式。

### 原型式继承

```js
function CreateObj(o) {
  function F() {}
  F.prototype = o;
  return new F();
}
```

上面就是 ES5 Object.create 的模拟实现，将传入的对象作为创建的对象的原型。
缺点：
包含引用类型的属性值始终都会共享相应的值，这点跟原型链继承一样。

```js
var person = {
  name: "kevin",
  friends: ["daisy", "kelly"]
};

var person1 = createObj(person);
var person2 = createObj(person);

person1.name = "person1";
console.log(person2.name); // kevin

person1.firends.push("taylor");
console.log(person2.friends); // ["daisy", "kelly", "taylor"]
```

注意：修改 person1.name 的值，person2.name 的值并未发生改变，并不是因为 person1 和 person2 有独立的 name 值，而是因为 person1.name = 'person1'，给 person1 添加了 name 值，并非修改了原型上的 name 值.

### 寄生式继承

创建一个仅用于封装继承过程的函数，该函数在内部以某种形式来做增强对象，最后返回对象。

```js
function createObj(o) {
  var clone = object.create(o);
  clone.sayName = function() {
    console.log("hi");
  };
  return clone;
}
```

缺点: 跟借用构造函数模式一样，每次创建对象都会创建一遍方法。

### 寄生组合式继承

组合继承最大的缺点就是虎调用两次父构造函数。
一次是设置子类型实例的原型的时候：

```js
Child.prototype = new Parent();
```

一次是在创建子类型实例的时候：

```js
var child1 = new Child("david", 23);
```

回想下 new 的模拟实现，其实在这句中，我们会执行：

```js
Parent.call(this, name);
```

在这里，我们又会调用了一次 Parent 构造函数。
所以，在这个例子中，如果我们打印 child1 对象，我们会发现 Child.prototype 和 child1 都有一个属性为 colors，属性值为['red', 'blue', 'green']。

那么我们该如何精益求精，避免这一次重复调用呢？

如果我们不使用 Child.prototype = new Parent() ，而是间接的让 Child.prototype 访问到 Parent.prototype 呢？

看看如何实现：

```js
function Parent(name) {
  this.name = name;
  this.colors = ["red", "cyan"];
}

Parent.prototype.getName = function() {
  console.log(this.name);
};

function Child(name, age) {
  Parent.call(this, name);
  this.age = age;
}

// 关键的三步
var F = function() {};
F.prototype = Parent.prototype;
Child.prototype = new F();

var Child1 = new Child("david", 23);

console.log(child1);
```

最后封装一下这个函数方法：

```js
function object(o) {
    functin F () {}
    F.prototype = o;
    return new F();
}

function prototype (child,parent) {
    var prototype = object(parent.prototype);
    prototype.constructor = child;
    child.prototype = prototype;
}

// 当我们使用的时候
prototype(Child,Parent);
```

引用《JavaScript 高级程序设计》中对寄生组合式继承的夸赞就是：

这种方式的高效率体现它只调用了一次 Parent 构造函数，并且因此避免了在 Parent.prototype 上面创建不必要的、多余的属性。与此同时，原型链还能保持不变；因此，还能够正常使用 instanceof 和 isPrototypeOf。开发人员普遍认为寄生组合式继承是引用类型最理想的继承范式。

> [原文地址](https://github.com/mqyqingfeng/Blog/issues/16)
