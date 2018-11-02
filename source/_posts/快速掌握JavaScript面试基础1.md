---
title: 快速掌握JavaScript面试基础1
date: 2018-02-20 18:15:33
categories: JavaScript
tags: 基础知识
---
### 类型和类型转换
在JavaScript中有7个内置类型：null,undefined,boolean,number,string,object和symbol（ES6)。

除了object以外，其他都叫做基本类型。
```js
typeof 0 //number
typeof true // boolean
typeof 'abc' // string
typeof null // object
typeof undefined // undefined
typeof Symbol('Hi') // symbol 
```
<!--more-->
* Null vs. Undefined

**Undefined**表示未定义。对于没有初始化的变量，函数调用时未提供的函数参数，不存在的对象属性，它们的默认值就是undefined。如果一个函数没有返回语句，那么默认的返回值也是undefined。

**Null**表示值为空。一个变量我们可以将其赋值为null,表示当前没有值。

* 隐式转换

```js
var name = 'Joey';
if (name) {
  console.log(name + " doesn't share food!")  // Joey doesn’t share food!
}
```
在if语句的条件判断中，name从字符串转换为布尔型。在if的代码块中，在控制台将name原原本本打印出来。你知道在什么情况下字符串会转换为真，什么时候为假么？

`""，0， null，undefined, NaN, false 会自动转换为false`。其它的都会转换为真：
```js
Boolean(null)         // false
Boolean('hello')      // true 
Boolean('0')          // true 
Boolean(' ')          // true 
Boolean([])           // true 
Boolean(function(){}) // true
```
空数组、对象、函数定义都会自动转换为真。

* String & Number之间的转换
* 
第一个你要非常小心的是`+`操作符。因为它同时用于数字相加和字符串拼接。

`*,/,-`只用于数字运算，当这些操作符和字符串一起使用，那么字符串会被强制转换为数字。
```js
1 + "2" = "12"
"" + 1 + 0 = "10"
"" - 1 + 0 = -1
"-9\n" + 5 = "-9\n5"
"-9\n" - 5 = -14
"2" * "3" = 6
4 + 5 + "px" = "9px"
"$" + 4 + 5 = "$45"
"4" - 2 = 2
"4px" - 2 = NaN
null + 1 = 1
```
* == vs. ===

一个广泛被接受的认知就是：==判断值是否相等，===同时判断值是否相等和类型是否相同。但是，这里有些误解。

实际上，==在验证相等性的时候，会对类型不同的值做一个类型转换。===对要判断的值不做类型转换。
```js
2 == '2'            // True
2 === '2'           // False
undefined == null   // True
undefined === null  // False
```
类型转换有很多取巧的地方，要注意：
```js
let a = '0';
console.log(Boolean(a)); // True
let b = false;
console.log(Boolean(b)); // False
```	
你认为下面的相等判断会输出什么值呢?
```js
console.log(a == b);
```
实际上会返回true。知道为什么吗？

**如果你将一个布尔类型的和非布尔类型的判断，JavaScript会将布尔类型的转换为数字然后再比对。**
执行过程如下：
```js
'0' == false   (1)
'0' == 0       (2)
 0  == 0       (3)
 ```
所以，最终变成了0==0，当然返回true啦。

一些比较容易掉坑的比较，我在这里列出来：
```js
false == ""  // true
false == []  // true
false == {}  // false
"" == 0      // true
"" == []     // true
"" == {}     // false
0 == []      // true
0 == {}      // false
0 == null    // false
```
### 值 vs. 引用
对于基本类型的值，赋值是通过值拷贝的形式；比如：null，undefined，boolean，number，string和ES6的symbol。对于复杂类型的值，通过引用拷贝的形式赋值。比如：对象、对象包括数组和函数。
```js
var a = 2;        // 'a' hold a copy of the value 2.
var b = a;        // 'b' is always a copy of the value in 'a'
b++;
console.log(a);   // 2
console.log(b);   // 3
var c = [1,2,3];
var d = c;        // 'd' is a reference to the shared value
d.push( 4 );      // Mutates the referenced value (object)
console.log(c);   // [1,2,3,4]
console.log(d);   // [1,2,3,4]
/* Compound values are equal by reference */
var e = [1,2,3,4];
console.log(c === d);  // true
console.log(c === e);  // false
```
如果想对复杂类型的值进行值拷贝，你需要自己去对所有子元素进行拷贝。
```js
const copy = c.slice()    // 'copy' 即使copy和c相同，但是copy指向新的值
console.log(c);           // [1,2,3,4]
console.log(copy);        // [1,2,3,4]
console.log(c === copy);  // false
```
### 作用域（Scope）
作用域是程序的执行环境，它包含了在当前位置可访问的变量和函数。

全局作用域是最外层的作用域，在函数外面定义的变量属于全局作用域，可以被任何其他子作用域访问。在浏览器中，window对象就是全局作用域。

局部作用域是在函数内部的作用域。在局部作用域定义的变量只能在该作用域以及其子作用域被访问。
```js
function outer() {
  let a = 1;
  function inner() {
    let b = 2;
    function innermost() {
      let c = 3;
      console.log(a, b, c);   // 1 2 3
    }
    innermost();
    console.log(a, b);        // 1 2 — 'c' is not defined
  }
  inner();
  console.log(a);             // 1 — 'b' and 'c' are not defined
}
outer();
```
你可以将作用域想象成一系列不断变小的门。如果一个个子不高的人可以穿过最小的门(局部最小作用域)，那么必然可以穿过任何比它大的门(外部作用域)。
### 提升(Hoisting)
在编译过程中，将var和function的定义移动到他们作用域最前面的行为叫做提升。

整个函数定义会被提升。所以，你可以在函数还未定义之前调用它，而不用担心找不到该函数。
```js
console.log(toSquare(3));  // 9

function toSquare(n){
  return n*n;
}
```
变量只会被部分提升。而且只有变量的声明会被提升，赋值不会动。

let和const不会被提升。
```js
{  /* Original code */
  console.log(i);  // undefined
  var i = 10
  console.log(i);  // 10
}

{  /* Compilation phase */
  var i;
  console.log(i);  // undefined
  i = 10
  console.log(i);  // 10
}
// ES6 let & const
{
  console.log(i);  // ReferenceError: i is not defined
  const i = 10
  console.log(i);  // 10
}
{
  console.log(i);  // ReferenceError: i is not defined
  let i = 10
  console.log(i);  // 10
}
```
### 函数表达式和函数声明
* 函数表达式

一个函数表达式是在函数执行到函数表达式定义的位置才开始创建，并被使用。它不会被提升。
```js
var sum = function(a, b) {
  return a + b;
}
```
* 函数声明

函数声明的函数可以在文件中任意位置调用，因为它会被提升。
```js
function sum(a, b) {
  return a + b;
}
```
### 变量：var，let和const
在ES6之前，只能使用var来声明变量。在一个函数体中声明的变量和函数，周围的作用域内无法访问。在块作用域if和for中声明的变量，可以在if和for的外部被访问。

注意：如果没有使用var,let或则const关键字声明的变量将会绑定到全局作用域上。
```js
function greeting() {
  console.log(s) // undefined
  if(true) {
    var s = 'Hi';
    undeclaredVar = 'I am automatically created in global scope';
  }
  console.log(s) // 'Hi'
}
console.log(s);  // Error — ReferenceError: s is not defined
greeting();
console.log(undeclaredVar) // 'I am automatically created in global scope'
```
ES6的let和const都是新引入的关键字。它们不会被提升，而且是块作用域。也就是说被大括号包围起来的区域声明的变量外部将不可访问。
```js
let g1 = 'global 1'
let g2 = 'global 2'
{   /* Creating a new block scope */
    g1 = 'new global 1'
    let g2 = 'local global 2'
    console.log(g1)   // 'new global 1'
    console.log(g2)   // 'local global 2'
    console.log(g3)   // ReferenceError: g3 is not defined
    let g3 = 'I am not hoisted';
}
console.log(g1)    // 'new global 1'
console.log(g2)    // 'global 2'
```
一个常见的误解是：使用const声明的变量，其值不可更改。准确地说它不可以被重新赋值，但是可以更改。
```js
const tryMe = 'initial assignment';
tryMe = 'this has been reassigned';  // TypeError: Assignment to constant variable.

// You cannot reassign but you can change it…
const array = ['Ted', 'is', 'awesome!'];
array[0] = 'Barney';
array[3] = 'Suit up!';
console.log(array);     // [“Barney”, “is”, “awesome!”, “Suit up!”]

const airplane = {};
airplane.wings = 2;
airplane.passengers = 200;
console.log(airplane);   // {passengers: 200, wings: 2}
```
[原文地址](https://blog.fundebug.com/2018/01/15/the-definitive-javascript-handbook-for-a-developer-interview/)