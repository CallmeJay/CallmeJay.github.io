---
title: ES6新特性
date: 2017-06-28 22:28:04
categories: JavaScript
tags: JavaScript
---
## ES6简介
​ 历时将近6年的时间来制定的新 ECMAScript 标准 ECMAScript 6（亦称 ECMAScript Harmony，简称 ES6）终于在 2015 年 6 月正式发布。自从上一个标准版本 ES5 在 2009 年发布以后，ES6 就一直以新语法、新特性的优越性吸引著众多 JavaScript 开发者，驱使他们积极尝鲜。
​由于ES6是在2015年发布的，所以也叫ES2015。
​以后ESCMAScript标准一年一更新，统一使用年份命名：ES2016、ES2017、....
<!--more-->
## 块级作用域
>在ES5之前，不存在块级作用域，在编程的时候很多时候会带来很多的不便，ES6新增了块级作用域，补足了这方面的缺陷。

块级声明指的是该声明的变量无法被代码块外部访问。块作用域，又被称为词法作用域（lexical scopes），可以在如下的条件下创建：
+ 函数内部
+ 在代码块（即 { }）内部

### let声明
>使用let声明变量的语法和使用var声明的语法是一样的。**但是let声明的变量的作用域会限制在当前的代码块中。这是let与var的最大区别。**

```js
let a = 10;
if(a > 5){
    console.log(b); //用let声明的变量没有声明提前这一特性，所以此处也访问不到（报错）
    let b = 20;
    console.log(b);
}
console.log(b); //由于b是在if块中使用let声明的，所以此处无法访问到。（报错）
```
**注意：**
1.用 let 声明的变量具有块级作用域，只能在声明的块中访问，在块外面无法访问
2.用let声明的变量也没有声明提前这一特性。
3.在同一个块中，let声明的变量也不能重复声明。
4.在声明变量的时候尽量使用let，慢慢的抛弃var
### const声明(Constant Declarations)
>在 ES6 使用const来声明的变量称之为常量。这意味着它们不能再次被赋值。由于这个原因，所有的 const 声明的变量都必须在声明处初始化。const声明的常量和let变量一样也是具有块级作用域的特性。

```js
var a = 20;
if (true) {
    const b = 20;
    b = 30;  //错误! 常量不能重新赋值
    const c; //错误！ 常量声明的同时必须赋值。
}
```
**注意:**
1.const的特性除了声明的是常量为，其他与let一样。
2.在let和const声明前的这段区域称之为暂存性死区（The Temporal Dead Zone —TDZ)。
3.使用let和const声明的变量和常量不再是window的属性。 也就是说通过window.a是无法访问到的。
### 循环中的块级绑定
>使用var声明的循环变量在循环结束后仍然可以访问到。 使用let声明的循环变量，在循环结束之后会立即销毁。

```js
for(let i = 0; i < 3; i++){ // 循环结束之后会立即销毁 i
    console.log(i);
}
console.log(i);  //此处无法访问到 i 。
```
### 循环中的函数
>看下面的代码，是输出10个10，而不是0，1，2，...

```js
var funcs = [];
for (var i = 0; i < 10; i++) {
    funcs.push(function () {
        console.log(i);
    });
}
funcs.forEach(function (func) {
    func();     // 输出 "10" 共10次
});
```
>解决办法需要使用函数的自执行特性。

```js
var funcs = [];
for (var i = 0; i < 10; i++) {
    funcs.push((function(value) {
        return function() {
            console.log(value);
        }
    }(i)));
}
funcs.forEach(function(func) {
    func();     // 输出 0，1，2 ... 9
});
```
**如果使用let声明变量，则完全可以避免前面的问题。 这是ES6规范中专门定义的特性。在for … in和for ... of循环中也适用**
```js
var funcs = [];
for (let i = 0; i < 10; i++) {
    funcs.push(function () {
        console.log(i);
    });
}
funcs.forEach(function (func) {
    func();     // 输出 0，1，2 ... 9
})
```
说明：
let 声明使得每次迭代都会创建一个变量 i，所以循环内部创建的函数会获得各自的变量 i 的拷贝。每份拷贝都会在每次迭代的开始被创建并被赋值。
## 函数的新增特性
### 带默认参数的函数
>JavaScript函数的最大的一个特点就是在传递参数的时候，参数的个数不受限制的。为了健壮性考虑，一般在函数内部需要做一些默认值的处理。

```js
function makeRequest(url, timeout, callback) {
    timeout = timeout || 2000;
    callback = callback || function() {};
}
```
其实上面的默认值方法有个bug：当timeout是0的时候也会当做假值来处理，从而给赋值默认值2000.
>ES6从语言层面面上增加了__默认值的__支持。看下面的代码：

```js
//这个函数如果只传入第一个参数，后面两个不传入，则会使用默认值。如果后面两个也传入了参数，则不会使用默认值。
function makeRequest(url, timeout = 2000, callback = function() {}) {
    // 其余代码
}
```
### 默认参数对 arguments 对象的影响
>在非严格模式下，arguments总是能反映出命名参数的变化。看下面的代码：

```js
function foo(a, b) {
    //非严格模式
    console.log(arguments[0] === a); //true
    console.log(arguments[1] === b); //true
    a = 10;
    b = 20;
    console.log(arguments[0] === a); //true
    console.log(arguments[1] === b); //true
}
foo(1, 2);
```
>在ES5的严格模式下，arguments只反映参数的初始值，而不再反映命名参数的变化！

```js
function foo(a, b) {
    //严格模式
    "use strict"
    console.log(arguments[0] === a); //true
    console.log(arguments[1] === b); //true
    a = 10;
    b = 20;
    console.log(arguments[0] === a); //false。  修改a的值不会影响到arguments[0]的值
    console.log(arguments[1] === b); //false
}
foo(1, 2);
```
>当使用ES6参数默认值的时候，不管是否是在严格模式下，都和ES5的严格模式相同。看下面的代码：

```js
function foo(a, b = 30) {
    console.log(arguments[0] === a); //true
    console.log(arguments[1] === b); //true
    a = 10;
    b = 20;
    console.log(arguments[0]  === a); //false。  由于b使用了默认值。虽然a没有使用默认值，但是仍然表现的和严格模式一样。
    console.log(arguments[1] === b); //false。  b使用了默认值，所以表现的和严格模式一样。
}
foo(1, 2);
```
>注意：如果这样调用foo(1),则 a == 1， b == 30， arguments[0] == 1, arguments[1] == undefined。也就是说默认值并不会赋值给arguments参数。

### 默认参数表达式 (Default Parameter Expressions)
>参数的默认值，也可以是一个表达式或者函数调用等。看下面的代码

```js
function getValue() {
    return 5;
}

function add(first, second = getValue()) { //表示使用getValue这个函数的返回值作为second的默认值。
    return first + second;
}

console.log(add(1, 1));     // 2.  调用add函数的时候，传入了第二个参数，则以传入的参数为准。
console.log(add(1));        // 6。 调用add函数的时候，没有传入第二个参数，则会调用getValue函数。
```
>有一点需要要注意：getValue()只会在调用add且不传入第二个参数的时候才会去调用。不是在解析阶段调用的。
>由于默认值可以表达式，所以我们甚至可以使用前面的参数作为后面参数的默认值。

```js
function add(first, second = first) {  // 使用第一个参数作为第二个参数的默认值
    return first + second;
 }
```
>注意：可以把前面的参数作为后面参数的默认值，但是不能把后面的参数作为第一个参数的默认值。这可以前面说的let和const的暂存性死区一个意思。

### 未命名参数问题
>Javascript并不限制传入的参数的数量。在调用函数的时候，传入的实参的个数超过形参的个数的时候，超过的部分就成为了未命名参数。在ES5之前，我们一般可以通过arguments对象来获取到未命名参数的值。但是罗显繁琐。

```js
function foo(a) {
    console.log(a);
    console.log(arguments[1])  //取得传入的多余的参数。
}
foo(2, 3);
``` 
>ES6，提供了一种更加优雅处理未命名参数的问题：**剩余参数( Rest Parameters)**
语法：function a(a, … b){ }
剩余参数使用三个点( … )和变量名来表示。

```js
function foo(a, ...b) {
    console.log(a);
    console.log(b instanceof Array);  //true  .多余的参数都被放入了b中。b其实就是一个数组。
}
foo(2, 3, 4, 6);
```
>注意：1.函数最多只能有一个剩余参数b。而且这个剩余参数必须位于参数列表的最后位置。
>2.虽然有了剩余参数，但是arguments仍然存在，但是arguments完全无视了剩余参数的存在。
>3.剩余参数是在函数声明的时候出现的。

### 函数中的扩展运算符
>例如:Math中的max函数可以返回任意多个参数中的最大值。但是如果这些参数在一个数组中，则没有办法直接传入。以前通用的做法是使用apply方法。

看下面的代码：
```js
let values = [25, 50, 75, 100]    
console.log(Math.max.apply(Math, values));  // 100
```
>上面这种方法虽然可行，但是总是不是那么直观
>使用ES6提供的扩展运算符可以很容易的解决这个问题。在数组前加前缀 … (三个点)。

```js
let values = [25, 50, 75, 100]
console.log(Math.max(...values));  //使用扩展运算符。相当于拆解了数组了。
console.log(Math.max(...values, 200));  //也可以使用扩展运算符和参数的混用，则这个时候就有 5 个数参与比较了。
```
>注意：剩余参数和扩展运算符都是 使用三个点作为前缀。但是他们使用的位置是不一样的。
>剩余参数是用在函数的声明的时候的参数列表中，而且必须在参数列表的后面
>扩展运算符是用在函数调用的时候作为实参来传递的，在实参中的位置没有限制。

## 全新的函数：箭头函数（=>）
### 箭头函数语法
> 基本语法如下：

```js
(形参列表)=>{
  //函数体
}
```
>箭头函数可以赋值给变量，也可以像匿名函数一样直接作为参数传递。

```js
var sum = (num1, num2) =>{
    return num1 + num2;
}
console.log(sum(3, 4));
//前面的箭头函数等同于下面的传统函数
var add = function (num1, num2) {
    return num1 + num2;
}
console.log(add(2, 4))
```
>如果函数体内只有一行代码，则包裹函数体的 大括号 ({ })完全可以省略。如果有return，return关键字也可以省略。
>如果函数体内有多条语句，则 {} 不能省略。

```js
var sum = (num1, num2) => num1 + num2;
console.log(sum(5, 4));
//前面的箭头函数等同于下面的传统函数
var add = function (num1, num2) {
    return num1 + num2;
}
console.log(add(2, 4));

//如果这一行代码是没有返回值的，则方法的返回自也是undefined
var foo = (num1, num2) => console.log("aaa");
console.log(foo(3,4));  //这个地方的返回值就是undefined
```
>如果箭头函数只有一个参数，则包裹参数的小括号可以省略。其余情况下都不可以省略。**当然如果不传入参数也不可以省略**

```js
var foo = a=> a+3; //因为只有一个参数，所以()可以省略
console.log(foo(4)); // 7
```
>如果想直接返回一个js对象，而且还不想添加传统的大括号和return，则必须给整个对象添加一个小括号 ()

```js
var foo = ()=>({name:"lisi", age:30});
console.log(foo());
//等同于下面的；
var foo1 = ()=>{
    return {
        name:"lisi",
        age : 30
    };
}
```
### 使用箭头函数实现函数自执行
```js
var person = (name => {
        return {
            name: name,
            age: 30
        }
    }
)("zs");
console.log(person);
```
### 箭头函数中无this绑定(No this Binding)
>在ES5之前this的绑定是个比较麻烦的问题，稍不注意就达不到自己想要的效果。因为this的绑定和定义位置无关，只和调用方式有关。
>**在箭头函数中则没有这样的问题，在箭头函数中，this和定义时的作用域相关，不用考虑调用方式**
>箭头函数没有 this 绑定，意味着 this 只能通过查找作用域链来确定。**如果箭头函数被另一个不包含箭头函数的函数囊括，那么 this 的值和该函数中的 this 相等，否则 this 的值为 window。**

```js
var PageHandler = {
    id: "123456",
    init: function () {
        document.addEventListener("click",
            event => this.doSomething(event.type), false); // 在此处this的和init函数内的this相同。
    },

    doSomething: function (type) {
        console.log("Handling " + type + " for " + this.id);
    }
};
PageHandler.init();
```
看下面的一段代码：
```js
var p = {
    foo:()=>console.log(this)   //此处this为window
}
p.foo();  //输出为 window对象。   并不是我想要的。所以在定义对象的方法的时候应该避免使用箭头函数。
//箭头函数一般用在传递参数，或者在函数内部声明函数的时候使用。
```
>说明：1.箭头函数作为一个使用完就扔的函数，不能作为构造函数使用。也就是不能使用new 的方式来使用箭头函数。
>2.由于箭头函数中的this与函数的作用域相关，所以不能使用call、apply、bind来重新绑定this。但是虽然this不能重新绑定，但是还是可以使用call和apply方法去执行箭头函数的。

### 无arguments绑定
>虽然箭头函数没有自己的arguments对象，但是在箭头函数内部还是可以使用它外部函数的arguments对象的。

```js
function foo() {
    //这里的arguments是foo函数的arguments对象。箭头函数自己是没有 arguments 对象的。
    return ()=>arguments[0]; //箭头函数的返回值是foo函数的第一个参数
}
var arrow = foo(4, 5);
console.log(arrow()); // 4
```
## 对象功能的扩展
>在JavaScript中，几乎所有的类型都是对象，所以使用好对象，对提示JavaScript的性能很重要。

### 对象类别
>ECMAScript 6 规范明确定义了每种对象类别。理解该术语对于从整体上认识该门语言显得十分重要。对象类别包括：

+ 普通对象（ordinary object）拥有 JavaScript 对象所有的默认行为。
+ 特异对象（exotic object）的某些内部行为和默认的有所差异。
+ 标准对象（standard object）是 ECMAScript 6 中定义的对象，例如 Array, Date 等，它们既可能是普通也可能是特异对象。
+ 内置对象（built-in object）指 JavaScript 执行环境开始运行时已存在的对象。标准对象均为内置对象。

### 对象字面量的语法扩展
#### 简写的属性初始化
```js
function createPerson(name, age) {
    //返回一个对象：属性名和参数名相同。
    return {
        name:name,
        age:age
    }
}
console.log(createPerson("lisi", 30)); // {name:"lisi", age:30}
```
>ES6中，上面的写法可以简化成如下形式：

```js
function createPerson(name, age) {
    //返回一个对象：属性名和参数名相同。
    return {
        name,  //当对象属性名和本地变量名相同时，可以省略冒号和值
        age
    }
}
console.log(createPerson("lisi", 30)); // {name:"lisi", age:30}
```
_当对象字面量中的属性只有属性名的时候，JavaScript 引擎会在该作用域内寻找是否有和属性同名的变量。在本例中，本地变量 name 的值被赋给了对象字面量中的 name 属性。_

#### 简写的方法声明
```js
var person = {
    name:'lisi',
    sayHell:function () {
        console.log("我的名字是：" + this.name);
    }
}
person.sayHell()
```
>在ES6中，上面的写法可以简化成如下的形式：

```js
var person = {
    name:'李四',
    sayHell() {
        console.log("我的名字是：" + this.name);
    }
}
person.sayHell()
```
#### 在字面量中动态计算属性名
>在ES5之前，如果属性名是个变量或者需要动态计算，则只能通过 对象.[变量名] 的方式去访问。而且这种动态计算属性名的方式 在字面量中 是无法使用的。
```js
var p = {
    name : '李四',
    age : 20
}
var attName = 'name';
console.log(p[attName]) //这里 attName表示的是一个变量名。
```
>而下面的方式使用时没有办法访问到attName这个变量的。

```js
var attName = 'name';
var p = {
    attName : '李四',  // 这里的attName是属性名，相当于各级p定义了属性名叫 attName的属性。
    age : 20
}
console.log(p[attName])  // undefined
```
>在ES6中，把属性名用[ ]括起来，则括号中就可以引用提前定义的变量。
```js
var attName = 'name';
var p = {
    [attName] : '李四',  // 引用了变量attName。相当于添加了一个属性名为name的属性
    age : 20
}
console.log(p[attName])  // 李四
```
### 新增的方法
>ECMAScript从第五版开始避免在Object.prototype上添加新的全局函数或方法，转而去考虑具体的对象类型（如数组）应该有什么方法。当某些方法不适合这些具体类型时就将它们添加到全局 Object 上 。
>ES6 在全局Object上添加了几个新的方法来轻松地完成一些特定任务。
#### Object.is()
>在 JavaSciprt 中当你想比较两个值时，你极有可能使用比较操作符（==）或严格比较操作符（===）。许多开发者为了避免在比较的过程中发生强制类型转换，更倾向于后者。但即使是严格等于操作符，它也不是万能的。例如，它认为 +0 和 -0 是相等的，虽然它们在 JavaScript 引擎中表示的方式不同。同样 NaN === NaN 会返回 false，所以必须使用 isNaN() 函数才能判断 NaN 。
>ECMAScript 6 引入了 Object.is() 方法来补偿严格等于操作符怪异行为的过失。该函数接受两个参数并在它们相等的返回 true 。只有两者在类型和值都相同的情况下才会判为相等。如下所示：

```js
console.log(+0 == -0);              // true
console.log(+0 === -0);             // true
console.log(Object.is(+0, -0));     // false

console.log(NaN == NaN);            // false
console.log(NaN === NaN);           // false
console.log(Object.is(NaN, NaN));   // true

console.log(5 == "5");              // true
console.log(5 === "5");             // false
console.log(Object.is(5, 5));       // true
console.log(Object.is(5, "5"));     // false
```
_很多情况下 Object.is() 的表现和 === 是相同的。它们之间的区别是前者 *认为 +0 和 -0 不相等而 NaN 和 NaN 则是相同的*。不过弃用后者是完全没有必要的。何时选择 Object.is() 与 == 或 === 取决于代码的实际情况。_
#### Object.assign()
>使用assign主要是为了简化对象的混入（mixin）。混入是指的在一个对象中引用另一个对象的属性或方法。
>assing可以把一个对象的属性和访问完整的转copy到另外一个对象中。

```js
var p = {
    name : "lisi",
    age : 20,
    friends : ['张三', '李四']
}
var p1 = {};
Object.assign(p1, p); //则p1中就有了与p相同的属性和方法.  p1是接受者，p是提供者
console.log(p1);
//这种copy是浅copy，也就是说如果属性值是对象的话，只是copy的对象的地址值(引用）
console.log(p1.friends == p.friends);  //true    p1和p的friends同事指向了同一个数组。
p.friends.push("王五");
console.log(p1.friends); //['张三', '李四', '王五']
```
>assign方法可以接受任意多的提供者。意味着后面提供者的同名属性会覆盖前面提供者的属性值。

```js
var p = {
    name : "lisi",
    age : 20,
    friends : ['张三', '李四']
}
var p1 = {
    name : 'zs',
}
var p2 = {};
Object.assign(p2, p, p1); //p和p1都是提供者
console.log(p2.name); // zs
```
## 字符串功能的增强
### 查找子字符串
>在以前在字符串中查找字符串的时候，都是使用indexOf方法。
>ES6新增了三个方法来查找字符串。
+ includes() 方法会在给定文本存在于字符串中的任意位置时返回 true，否则返回 false 。
+ startsWith() 方法会在给定文本出现在字符串开头时返回 true，否则返回 false 。
+ endsWith() 方法会在给定文本出现在字符串末尾时返回 true，否则返回 false 。

>每个方法都接收两个参数：需要搜索的文本和可选的起始索引值。当提供第二个参数后，includes() 和 startsWith() 会以该索引为起始点进行匹配，而 endsWith() 将字符串的长度与参数值相减并将得到的值作为检索的起始点。若第二个参数未提供，includes() 和 startsWith() 会从字符串的起始中开始检索，endsWith() 则是从字符串的末尾。实际上，第二个参数减少了需要检索的字符串的总量。以下是使用这些方法的演示：

```js
var msg = "Hello world!";

console.log(msg.startsWith("Hello"));       // true
console.log(msg.endsWith("!"));             // true
console.log(msg.includes("o"));             // true

console.log(msg.startsWith("o"));           // false
console.log(msg.endsWith("world!"));        // true
console.log(msg.includes("x"));             // false

console.log(msg.startsWith("o", 4));        // true
console.log(msg.endsWith("o", 8));          // true
console.log(msg.includes("o", 8));          // false
```
### repeat方法
>ECMAScript 6 还向字符串添加了 repeat() 方法，它接受一个数字参数作为字符串的重复次数。该方法返回一个重复包含初始字符串的新字符串，重复次数等于参数。例如：

```js
console.log("x".repeat(3));         // "xxx"
console.log("hello".repeat(2));     // "hellohello"
console.log("abc".repeat(4));       // "abcabcabcabc"
```

### 字符串模板字面量
>模板字面量是 ECMAScript 6 针对 JavaScript 直到 ECMAScript 5 依然缺失的如下功能的回应：
>+ 多行字符串 针对多行字符串的形式概念（formal concept）。
>+ 基本的字符串格式化 将字符串中的变量置换为值的能力。
>+ 转义 HTML 能将字符串进行转义并使其安全地插入到 HTML 的能力。

>模板字面量以一种全新的表现形式解决了这些问题而不需要向 JavaScript 已有的字符串添加额外的功能。

#### 基本语法
>使用一对反引号 ``(tab正上方的按键)来表示模板字面量。

```js
let message = `Hello world!`;   //使用模板字面量创建了一个字符串

console.log(message);               // "Hello world!"
console.log(typeof message);        // "string"
console.log(message.length);        // 12
```
#### 多行字符串
>在ES5之前JavaScript是不支持多行字符串的。（但是在以前的版本中有一个大家都认为是bug的方式可以写出多行字符串，就是在尾部添加一个反斜杠 \）

```js
var s = "abc \
aaaaaa";
console.log(s); //但是输出的结果中不包括换行
```
>但是在ES6中字符串的模板字面量轻松的解决了多行字符串的问题，而且没有任何新的语法.

```js
var s = `abc
aaaaa
dsalfja
dfadfja`;
console.log(s);
```
>但是要注意： 反引号中的所有空格和缩进都是有效字符。

#### 字符串置换
>置换允许你将 JavaScript 表达式嵌入到模板字面量中并将其结果作为输出字符串中的一部分。
>语法：**${变量名、表达式、任意运算、方法调用等}**
>可以嵌入任何有效的JavaScript代码

```js
var name = "李四";
var msg = `欢迎你${name}同学`;
console.log(msg)
```
#### 模板标签
##### 什么是模板标签
>模板字面量真正的强大之处来源于模板标签。一个模板标签可以被转换为模板字面量并作为最终值返回。标签在模板的头部，即左 ` 字符之前指定，如下所示：

```js
let message = myTag`Hello world`;
```
>在上面的代码中，myTag就是模板标签。
>myTag其实是一个函数，这个函数会被调用来处理这个模板字符串。

##### 定义模板标签
>一个标签仅代表一个函数，他接受需要处理的模板字面量。标签分别接收模板字面量中的片段，且必须将它们组合以得出结果。函数的首个参数为包含普通 JavaScript 字符串的数组。余下的参数为每次置换的对应值。
>标签函数一般使用剩余参数来定义，以便轻松地处理数据。如下：
```js
let name = '张三',
    age = 20,
    message = show`我来给大家介绍${name}的年龄是${age}.`;

/*
    应该定义一个函数show：
    参数1：一个字符串数组。在本例中包含三个元素。
            0:"我来给大家介绍"
            1:"的年龄是"
            2:"."
    参数2和参数3：表示需要置换的字符串的值。  
    */
function show(stringArr, value1, value2) {
    console.log(stringArr); //
    console.log(value1);  // 张三
    console.log(value2);  // 20
        return "abc";
}
console.log(message); //abc
```
>为了简化书写，一般把Value1和Value2写成剩余字符串的形式

```js
function show(stringArr, ...values){
  // blabla...
}
```

