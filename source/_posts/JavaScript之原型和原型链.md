---
title: JavaScript之原型和原型链
date: 2018-02-08 22:02:47
categories: JavaScript
tags: 原型
---
### 构造函数创建对象
我们先使用构造函数创建一个对象：
```js
function Person() {

}
var person = new Person();
person.name = 'lili';
console.log(person.name); // lili
```
在这个例子中，Person就是一个构造函数，我们使用new创建了一个实例对象person。
<!--more-->
### prototype
每一个**函数都有一个prototype属性**，就是我们经常在各种例子中看到那个prototype,比如：
```js
function Person() {

}
/**
*虽然写在注释了，但是要注意：prototype是函数才会有的属性
*/
Person.prototype.name = 'Jay';
var person1 = new Person();
var person2 = new Person();
console.log(person1.name); // jay
console.log(person2.name); // jay
```
那这个函数的prototype属性到底指向的是什么呢？

其实，函数的prototype属性指向了一个对象，这个对象正是**调用该构造函数而创建的实例**的原型，也就是这个例子中的person1和person2的原型。

那什么是原型呢？可以这样理解：每一个JavaScript对象（null除外）在创建的时候就会与之关联另一个对象，这个对象就是我们所说的原型，每一个对象都会从原型‘继承’属性。

让我们用一张图表示构造函数和实例原型之间的关系：
![](http://oonulpk6h.bkt.clouddn.com/prototype1.png)
在这张图中我们用的`Object.prototype`表示实例原型。

那么我们该怎么表示实例与实例原型，也就是person和Person.prototype之间的关系呢？
### \__proto__
这是每一个JavaScript对象（null除外）都具有的一个属性，叫\__proto__，这个属性会指向该对象的原型，证明如下：
```js
function Person() {

}
var person = new Person();
console.log(person.__proto__ === Person.prototype); // true
```
更新关系图：
![](http://oonulpk6h.bkt.clouddn.com/prototype2.png)
既然实例对象和构造函数都可以指向原型，那么原型是否有属性指向构造函数或者实例呢？
### constructor
指向实例倒是没有，因为一个构造函数可以生成多个实例，但是原型指向构造函数倒是有的，这就是第三个属性: constructor，每个原型都有一个constructor属性指向关联的构造函数。
```js
function Person() {

}
console.log(Person === Object.prototype.constructor);
```
再更新关系图：
![](http://oonulpk6h.bkt.clouddn.com/prototype3.png)
综上所述:
```js
function Person() {

}
var person = new Person();

console.log(person.__proto__ === Person.prototype); // true
console.log(Person.prototype.constructor = Person); // true
// 顺便学习一个ES5的方法,可以获得对象的原型
console.log(Object.getPrototypeOf(person) === Person.prototype); // true
```
了解了构造函数、实例原型、和实例之间的关系，接下来我们讲讲实例和原型的关系。
### 实例与原型
当读取实例的属性时，如果找不到，就会查找与对象关联的原型中的属性，如果还查不到，就去找原型的原型，一直找到最顶层为止。
举个例子：
```js
function Person(){

}
Person.prototype.name = 'jay';

var person = new Person();

person.name = 'John';
console.log(person.name); // John

delete person.name;
console.log(person.name); // jay
```
在这个例子中，我们给实例对象person添加了name属性，当我们打印person.name的时候，结果自然为John。

但是当我们删除了person的name属性时，读取person.name,从person对象中找不到name属性就会从person的原型也就是person.\__proto__,也就是Person.prototype中查找，幸运的是我们找打name属性，结果为jay。

但是万一没找到呢，原型的原型又是什么呢？
### 原型的原型
在前面，我们已经讲了原型也是一个对象，既然是对象，那我们就可以用最原始的方式创建它：
```js
var obj = new Object();
obj.name = 'Kevin';
console.log(obj.name); // Kevin
```
其实原型对象就是通过Object构造函数生成的，结合之前所讲，实例的\__proto__指向构造函数的prototype,所以载更新关系图：
![](http://oonulpk6h.bkt.clouddn.com/prototype4.png)
### 原型链
那Object.prototype的原型呢？
null, 我们可以打印：
```js
console.log(Object.prototype.__proto__ === null); // true
```
然而null究竟代表了什么呢？
> null代表’没有对象’，即该处不该有值。

![](http://oonulpk6h.bkt.clouddn.com/null&undefined.png)
所以 Object.prototype.\__proto__的值为 null 跟 Object.prototype 没有原型，其实表达了一个意思。

所以查找属性的时候查到 Object.prototype 就可以停止查找了。

最后一张关系图：
![](http://oonulpk6h.bkt.clouddn.com/prototype5.png)

顺便还要说一下，图中由相互关联的原型组成的链状结构就是**原型链**，也就是蓝色的这条线。
### 补充
#### constructor
首先是constructor属性，例子：
```js
function Person () {

}
var person = new Person();
console.log(person.constructor == Person); // true
```
当获取person.constructor时，其实person中并没有constructor属性，当不能读取到constructor属性时，会从person的原型也就是Person.prototype中读取，正好原型中有这个属性，所以：
```js
person.constructor = Person.prototype.constructor
```
#### \__proto__
其次是\_\_proto\_\_ ，绝大部分浏览器都支持这个非标准的方法访问原型，然而它并不存在于 Person.prototype 中，实际上，它是来自于 Object.prototype ，与其说是一个属性，不如说是一个 getter/setter，当使用 obj.\__proto__ 时，可以理解成返回了 Object.getPrototypeOf(obj)。
#### 真的是继承吗？
最后是关于继承，前面我们讲到“每一个对象都会从原型’继承’属性”，实际上，继承是一个十分具有迷惑性的说法， 引用《你不知道的JavaScript》中的话，就是：

继承意味着复制操作，然而JavaScript默认并不会复制对象的属性，相反，JavaScript只是在两个对象之间创建了一个关联，这样，一个对象就可以通过委托访问另一个对象的属性和方法，所以与其叫继承，委托的说法反而更准确些。

