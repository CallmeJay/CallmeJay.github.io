---
title: 快速掌握JavaScript面试基础3
date: 2018-02-21 16:49:21
categories: JavaScript
tags: 基础知识
---

### new 关键字

如果使用 new 关键字来调用函数式很特别的形式。我们把那些用 new 调用的函数叫做构造函数(constructor function)。

使用了 new 的函数到底做了什么事情呢？

- 创建一个新的对象
- 将对象的 prototype 设置为构造函数的 prototype
- 执行构造函数，this 执行新构造的对象
- 返回该对象。如果构造函数返回对象，那么返回该构造对象。
  <!--more-->

```js
// 为了更好地理解底层，我们来定义new关键字
function myNew(constructor, ...arguments) {
  var obj = {};
  Object.setPrototypeOf(obj, constructor.prototype);
  return constructor.apply(obj, arguments) || obj;
}
```

使用 new 和不使用的区别在哪里呢？

```js
function Bird() {
  this.wings = 2;
}
/* 普通的函数调用 */
let fakeBird = Bird();
console.log(fakeBird); // undefined
/* 使用new调用 */
let realBird = new Bird();
console.log(realBird); // { wings: 2 }
```

为了便于对比理解，译者额外增加了测试了一种情况：

```js
function MBird() {
  this.wings = 2;
  return "hello";
}

let realMBrid = new MBird();
console.log(realMBird); // { wings: 2 }
```

你会发现，这一句 return "hello"并没有生效！

### 原型和继承

原型(Prototype)是 JavaScript 中最容易搞混的概念，其中一个原因是 prototype 可以用在两个不同的情形下。

- 原型关系
  每一个对象都有一个 prototype 对象，里面包含了所有它的原型的属性。
  .\_\_proto\_\_是一个不正规的机制(ES6 中提供)，用来获取一个对象的 prototype。你可以理解为它指向对象的 parent。
  所有普通的对象都继承.constructor 属性，它指向该对象的构造函数。当一个对象通过构造函数实现的时候，\_\_proto\_\_属性指向构造函数的构造函数的.prototype。Object.getPrototypeOf()是 ES5 的标准函数，用来获取一个对象的原型。

- 原型属性
  每一个函数都有一个.prototype 属性，它包含了所有可以被继承的属性。该对象默认包含了指向原构造函数的.constructor 属性。每一个使用构造函数创建的对象都有一个构造函数属性。

接下来通过例子来帮助理解：

```js
function Dog(breed, name) {
  (this.breed = breed), (this.name = name);
}
Dog.prototype.describe = function() {
  console.log(`${this.name} is a ${this.breed}`);
};
const rusty = new Dog("Beagle", "Rusty");

/* .prototype 属性包含了构造函数以及构造函数中在prototype上定义的属性。*/
console.log(Dog.prototype); // { describe: ƒ , constructor: ƒ }

/* 使用Dog构造函数构造的对象 */
console.log(rusty); //  { breed: "Beagle", name: "Rusty" }

/* 从构造函数的原型中继承下来的属性或函数 */
console.log(rusty.describe()); // "Rusty is a Beagle"

/* .__proto__ 属性指向构造函数的.prototype属性 */
console.log(rusty.__proto__); // { describe: ƒ , constructor: ƒ }

/* .constructor 属性指向构造函数 */
console.log(rusty.constructor); // ƒ Dog(breed, name) { ... }
```

### 原型链

原型链是指对象之间通过 prototype 链接起来，形成一个有向的链条。当访问一个对象的某个属性的时候，JavaScript 引擎会首先查看该对象是否包含该属性。如果没有，就去查找对象的 prototype 中是否包含。以此类推，直到找到该属性或则找到最后一个对象。最后一个对象的 prototype 默认为 null。

### 拥有 vs 继承

一个对象有两种属性，分别是它自身定义的和继承的。

```js
function Car() {}
Car.prototype.wheels = 4;
Car.prototype.airbags = 1;

var myCar = new Car();
myCar.color = "black";

/*  原型链中的属性也可以通过in来查看:  */
console.log("airbags" in myCar); // true
console.log(myCar.wheels); // 4
console.log(myCar.year); // undefined

/*  通过hasOwnProperty来查看是否拥有该属性:  */
console.log(myCar.hasOwnProperty("airbags")); // false — Inherited
console.log(myCar.hasOwnProperty("color")); // true
```

Object.create(obj) 创建一个新的对象，prototype 指向 obj。

```js
var dog = { legs: 4 };
var myDog = Object.create(dog);

console.log(myDog.hasOwnProperty("legs")); // false
console.log(myDog.legs); // 4
console.log(myDog.__proto__ === dog); // true
```

### 继承是引用传值

继承属性都是通过引用的形式。看例子：

```js
var objProt = { text: "original" };
var objAttachedToProt = Object.create(objProt);
console.log(objAttachedToProt.text); // original

// 我们更改objProt的text属性，objAttachedToProt的text属性同样更改了
objProt.text = "prototype property changed";
console.log(objAttachedToProt.text); // prototype property changed

// 但是如果我们讲一个新的对象赋值给objProt，那么objAttachedToProt的text属性不受影响
objProt = { text: "replacing property" };
console.log(objAttachedToProt.text); // prototype property changed
```

### 异步 JavaScript

JavaScript 是一个单线程程序语言，也就是说 JavaScript 引擎一次只能执行某一段代码。它导致的问题就是：如果有一段代码需要耗费很长的时间执行，其它的操作就被卡住了。JavaScript 使用 Call Stack 来记录函数的调用。一个 Call Stack 可以看成是一摞书。最后一本书放在最上面，也最先被移走。最先放的书在最底层，最后被移走。

为了避免复杂代码占用 CPU 太长时间，一个解法就是定义异步回调函数。我们自己来定义一个异步函数看看：

```js
function greetingAsync(name, callback) {
  let greeting = "hello, " + name;
  setTimeout(_ => callback(greeting), 0);
}

greetingAsync("fundebug", console.log);
console.log("start greeting");
```

我们在 greetingAsync 中构造了 greeting 语句，然后通过 setTimeout 定义了异步，callback 函数，是为了让用户自己去定义 greeting 的具体方式。为方便起见，我们时候直接使用 console.log。

上面代码执行首先会打印 start greeting，然后才是 hello, fundebug。也就是说，greetingAsync 的回调函数后执行。在网站开发中，和服务器交互的时候需要不断地发送各种请求，而一个页面可能有几十个请求。如果我们一个一个按照顺序来请求并等待结果，串行的执行会使得网页加载很慢。通过异步的方式，我们可以先发请求，然后在回调中处理请求结果，高效低并发处理。

下面通过一个例子来描述整个执行过程：

```js
const first = function() {
  console.log("First message");
};
const second = function() {
  console.log("Second message");
};
const third = function() {
  console.log("Third message");
};

first();
setTimeout(second, 0);
third();

// 输出:
// First message
// Third message
// Second message
```

1. 初始状态下，浏览器控制台没有输出，并且事件管理器(Event Manager)是空的；
2. first()被添加到调用栈
3. 将 console.log("First message")加到调用栈
4. console.log("First message")执行并输出“First message”到控制台
5. console.log("First message")从调用栈中移除
6. first()从调用栈中移除
7. setTimeout(second, 0)加到调用栈
8. setTimeout(second, 0)执行，0ms 之后，second()被加到回调队列
9. setTimeout(second, 0)从调用栈中移除
10. third()加到调用栈
11. console.log("Third message")加到调用栈
12. console.log("Third message")执行并输出“Third message”到控制台
13. console.log("Third message")从调用栈中移除
14. third()从调用栈中移除
15. Event Loop 将 second()从回调队列移到调用栈
16. console.log("Second message")加到调用栈
17. console.log("Second message")Second message”到控制台
18. console.log("Second message")从调用栈中移除
19. Second()从调用栈中移除

特别注意的是：second()函数在 0ms 之后并没有立即执行，你传入到 setTimeout()函数的时间和 second()延迟执行的时间并不一定直接相关。事件管理器等到 setTimeout()设置的时间到期才会将其加入回调队列，而回调队列中它执行的时间和它在队列中的位置以及它前面的函数的执行时间有关。

[原文地址](https://blog.fundebug.com/2018/01/29/the-definitive-javascript-handbook-for-a-developer-interview-3/)
