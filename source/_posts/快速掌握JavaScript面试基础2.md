---
title: 快速掌握JavaScript面试基础2
date: 2018-02-21 12:47:09
categories: JavaScript
tags: 基础知识
---

### 闭包

闭包由一个函数以及该函数定义式所在的环境组成。

```js
function sayHi(name) {
  var message = `Hi ${name}`;
  function greeting() {
    console.log(message);
  }
  return greeting();
}
var sayHiToJon = sayHi("Jon");
console.log(sayHiToJon); // ƒ() { console.log(message) }
console.log(sayHiToJon()); // 'Hi Jon!'
```

<!--more-->

请理解`var sayHiToJon=sayHi('Jon')`;这段代码的执行过程，sayHi 函数执行，首先将 message 的值计算出来，然后定义了 greeting 函数，函数中引用了 message 变量，最后，返回 greeting 函数。
如果按照 C/Java 语言的思路，sayHiToJon 就等价于 greeting 函数，那么就会报错：message 为定义。但是在 JavaScript 中不一样，这里的 sayHiToJon 函数等于 greeting 函数以及一个环境，该环境中包含了 message。因此，当我们调用 sayHiToJon 函数，可以成功将 message 打印出来。因此，这里的闭包就是 greeting 函数和一个包含 message 变量的环境。

闭包的一个优势在于**数据隔离**，下面用一个例子说明：

```js
function SpringfieldSchool() {
  let staff = ["Seymour Skinner", "Edna Krabappel"];
  return {
    getStaff: function() {
      console.log(staff);
    },
    addStaff: function(name) {
      staff.push(name);
    }
  };
}

let elementary = SpringfieldSchool();
console.log(elementary); // { getStaff: ƒ, addStaff: ƒ }
console.log(staff); // ReferenceError: staff is not defined
/* Closure allows access to the staff variable */
elementary.getStaff(); // ["Seymour Skinner", "Edna Krabappel"]
elementary.addStaff("Otto Mann");
elementary.getStaff(); // ["Seymour Skinner", "Edna Krabappel", "Otto Mann"]
```

在 elementary 被创建的时候，SpringfieldSchool 已经返回。也就是说 staff 无法被外部访问。唯一可以访问的方式就是里面的闭包函数 getStaff 和 addStaff。

我们来看一个面试题：下面的代码有什么问题，如何修复？

```js
const arr = [10, 12, 15, 21];
for (var i = 0; i < arr.length; i++) {
  setTimeout(function() {
    console.log(`The value ${arr[i]} is at index: ${i}`);
  }, (i + 1) * 1000);
}
```

上面的代码输出的结果全部都一样：”The value undefined is at index: 4”。因为所有在 setTimeout 中定义的匿名函数都引用了同一个外部变量 i。当匿名函数执行的时候，i 的值为 4。

这个问题可以改用 IIFE(后面会介绍)方法来解决，通过对每一个匿名函数构建独立的外部作用域来实现。

```js
const arr = [10, 12, 15, 21];
for (var i = 0; i < arr.length; i++) {
  (function(j) {
    setTimeout(function() {
      console.log(`The value ${arr[j]} is at index: ${j}`);
    }, j * 1000);
  })(i);
}
```

当然，还有一个方法，使用 let 来声明 i。

```js
const arr = [10, 20, 30, 40];
for (let i = 0; i < arr.length; i++) {
  setTimeout(function() {
    console.log("The value ${arr[i]} is at index: ${i}");
  }, i * 1000);
}
```

### 立即调用的函数表达式(Immediate Invoked Function Expression)(IIFE)

一个 IIFE 是一个函数表达式在定义之后立即被调用。常用在你想对一个新声明的变量创建一个隔离的作用域。
它的格式为:`(function(){....})()`。前面的大括号用于告诉编译器这里不仅仅是函数定义，后面的大括号用于执行该函数。

```js
var result = [];
for (var i = 0; i < 5; i++) {
  result.push(function() {
    return i;
  });
}
console.log(result[1]()); // 5
console.log(result[3]()); // 5
result = [];
for (var i = 0; i < 5; i++) {
  (function() {
    var j = i; // copy current value of i
    result.push(function() {
      return j;
    });
  })();
}
console.log(result[1]()); // 1
console.log(result[3]()); // 3
```

使用 IIFE 可以：

- 为函数绑定私有数据
- 创建一个新的环境
- 避免污染全局命名空间

### 环境（Context）

我们往往容易将环境（Context）和作用域（Scope）搞混，解释一下：

- **环境（Context）**：由函数如何被调用而决定，往往指 this.
- **作用域（Scope）**：可访问的变量。

### 函数调用：call, apply, bind

这三个方法都是为了将 this 绑定到函数，区别在于调用的方式。

- .call(a,b,c,d...)会理解执行函数，需要把参数按照顺序传入；
- .apply([a,b,c,d...])会理解执行函数，需要把参数组合成数组传入了；

.call()和.apply()几乎相同。哪个传入参数方便，你就选择哪个。

```js
const Snow = { surename: "Snow" };
const char = {
  surename: "Stark",
  knows: function(arg, name) {
    console.log(`You know ${arg}, ${name} ${this.surename}`);
  }
};
char.knows("something", "Bran"); // You know something, Bran Stark
char.knows.call(Snow, "nothing", "Jon"); // You know nothing, Jon Snow
char.knows.apply(Snow, ["nothing", "Jon"]); // You know nothing, Jon Snow
```

注意：如果你将数组传入 call 函数，它会认为只有一个参数。

ES6 允许使用新的操作符将数组变换为一个序列。

```js
char.knows.call(Snow, ...["nothing", "Jon"]); // You know nothing, Jon Snow
```

.bind()返回一个新的函数，以及相应的环境和参数。如果你想该函数稍后调用，那么推荐使用 bind。
.bind()函数的优点在于它可以记录一个执行环境，对于异步调用和事件驱动的编程很有用。

.bind()传参数的方式和 call 相同。

```js
const Snow = { surename: "Snow" };
const char = {
  surename: "Stark",
  knows: function(arg, name) {
    console.log(`You know ${arg}, ${name} ${this.surename}`);
  }
};
const whoKnowsNothing = char.knows.bind(Snow, "nothing");
whoKnowsNothing("Jon"); // You know nothing, Jon Snow
```

### this 关键字

要理解 JavaScript 中 this 关键字，特别是它指向谁，有时候相当地复杂。this 的值通常由函数的执行环境决定。简单的说，执行环境指函数如何被调用的。this 像是一个占位符(placeholder)，它指向当方法被调用时，调用对应的方法的对象。

下面有序地列出了判断 this 指向的规则。如果第一条匹配，那么就不用去检查第二条了。

#### new 绑定

当使用 new 关键字调用函数的时候，this 指向新构建的对象(实例)。

```js
function Person(name,age){
  this.name = name;
  this.age = age;
  console.log(this);
}
const = Rachel = new Person('Rachel',30); // {name: 'Rachel',age: 30}
```

#### 显示绑定(Explicit binding)

当使用 call 或则 apply 的时候，我们显示的传入一个对象参数，该参数会绑定到 this。 注意：.bind()函数不一样。用 bind 定义一个新的函数，但是依然绑定到原来的对象。

```js
function fn() {
  console.log(this);
}
var agent = { id: "007" };
fn.call(agent); // { id: '007' }
fn.apply(agent); // { id: '007' }
var boundFn = fn.bind(agent);
boundFn(); // { id: '007' }
```

#### 隐式绑定

当一个函数在某个环境下调用(在某个对象里)，this 指向该对象。也就是说该函数是对象的一个方法。

```js
var building = {
  floors: 5,
  printThis: function() {
    console.log(this);
  }
};
building.printThis(); // { floors: 5, printThis: function() {…} }
```

#### 默认绑定

如果上面所有的规则都不满足，那么 this 指向全局对象(在浏览器中，就是 window 对象)。当函数没有绑定到某个对象，而单独定义的时候，该函数默认绑定到全局对象。

```js
function printWindow() {
  console.log(this);
}
printWindow(); // window object
```

注意：下面的情况中，inner 函数中的 this 指向全局。

```js
function Dinosaur(name) {
  this.name = name;
  var self = this;
  inner();
  function inner() {
    alert(this); // window object — the function has overwritten the 'this' context
    console.log(self); // {name: 'Dino'} — referencing the stored value from the outer context
  }
}
var myDinosaur = new Dinosaur("Dino");
```

#### 词法(Lexical) this

当是使用`=>`来定义函数时，this 指向定义该函数时候外层的 this。
备注：大概是和定义的词法(=>)有关，把它称作 Lexical this。

```js
function Cat(name) {
  this.name = name;
  console.log(this); // { name: 'Garfield' }
  (() => console.log(this))(); // { name: 'Garfield' }
}
var myCat = new Cat("Garfield");
```

[原文地址](https://blog.fundebug.com/2018/01/22/the-definitive-javascript-handbook-for-a-developer-interview-2/)
