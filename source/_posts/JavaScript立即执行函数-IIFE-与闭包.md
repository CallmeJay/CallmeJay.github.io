---
title: JavaScript立即执行函数(IIFE)与闭包
date: 2018-06-22 18:08:02
categories: JavaScript
tags: 闭包
---

### 前言

一直没搞清楚立即执行函数和闭包之间的关系，总结一下：

1. 闭包有很多种理解：访问不到内部作用域，函数就是这样，所以函数就是闭包；
2. 闭包还有一种理解：通过把函数内部的变量和方法返回出来，这样外部作用域就可以访问内部作用域了，并且立即执行函数和闭包之间没有必然的联系，虽然它们经常结合一起使用；
3. 立即执行函数只是一种函数的调用方式；
4. 闭包的目的则是外部函数可以访问内部函数的作用域；

<!-- more -->

### 立即执行函数（IIFE）

立即执行函数是指声明完之后便直接执行的函数，这类函数通常是一次性使用的，因此没必要给这类函数命名，直接让它执行就好了；

一般情况下，只对匿名函数使用这种 **立即执行函数** 。它的核心是 **闭包** ，实现的目的有以下几个：

* 不必为函数命名，避免污染全局变量；
* *内部形成单独的块级作用域，封装一些私有变量；
* 内部变量执行完即销毁，不会占用更多的内存。

javascript 中没用私有作用域的概念，如果在多人开发的项目上，你在全局或局部作用域中声明了一些变量，可能会被其他人不小心用同名的变量给覆盖掉，根据 javascript 函数作用域链的特性，可以使用 IIFE 可以模仿一个私有作用域，用匿名函数作为一个“容器”，“容器”内部可以访问外部的变量，而外部环境不能访问“容器”内部的变量，所以( function(){…} )()内部定义的变量不会和外部的变量发生冲突，俗称“匿名包裹器”或“命名空间”。

定义一个函数，函数后面加上括号，即可完成调用；

```js
function (){}();   // SyntaxError: Unexpected token (
```

引擎在遇到关键字 function 时，会默认将其当做是一个函数声明，函数声明必须有一个函数名，所以在执行到第一个左括号时就报语法错误了；

然后我们给它加上函数名:

```js
function abc(){}(); // SyntaxError: Unexpected token )
//在一个表达式后面加上括号，表示该表达式立即执行；
//而如果是在一个语句后面加上括号，该括号完全和之前的语句没什么关系，而只是一个分组操作符，用来控制运算中的优先级（小括号里的先运算）。
```

上面代码可以写成：

```js
function abc(){}; ();// SyntaxError: Unexpected token )
//分组操作符内的表达式不能为空，执行到右括号时，发现表达式为空，所以报错。
```

然而函数表达式的函数名则可有可无；

在 function 前面加！、+、 -、=甚至是逗号等或者把函数用（）包起来都可以将函数声明转换成函数表达式；我们一般用（）把函数声明包起来或者用 = ；

我们在函数后面加上括号，然后再用另一个括号把它们都包起来；

javascript 中，括号内不允许包含语句，但可以是表达式；

引擎先遇到括号，然后遇到关键字 function , 就自动把括号里面的代码识别为函数表达式，而不是函数声明；

```js
(function (){/*code*/}());
//javascript中，括号内不允许包含语句，但可以是表达式；
//引擎先遇到括号，然后遇到关键字function , 就自动把括号里面的代码识别为函数表达式，而不是函数声明 
(function(){/*code*/})(); //也可以这样写

var a = function(){/*code*/}();
//我们可以在函数表达式后面直接加括号，而不用把函数包起来；

//但还是推荐外部加上()：
var b = (function(){/*code*/}());
var c = (function(){/*code*/})();
//因为外部如果没有()，我们得去function(){/*code*/}后面看是否存在()，判断b/c是一个函数还是一个函数内部的返回值
//为了代码的可读性，还是要在外部加上()，无论是否已经是表达式；
```

javascript 只有函数拥有局部作用域，立即执行函数也有这一特点，我们可以利用它减少全局变量造成的空间污染；

```js
(function abc(){
    console.log(abc);//function abc(){...}
    var a = 1;
    console.log(a);//1
})()

console.log(abc);//ReferenceError: abc is not defined
//函数表达式的标识符在外部作用域是找不到的，只有内部作用域可以找到
//立即执行函数的函数名在外部也是找不到的
```

### 闭包(closure)

闭包通常用来创建内部变量，使得这些变量不能被外部随意修改，同时又可以通过指定的函数接口来操作；简单的说，外部作用域就可以访问函数内部作用域的变量了。

由于作用域的关系，我们在函数外部是无法直接访问到函数内部的变量的，但是函数内部可以把这个变量传给全局变量或者返回出来，这样外部作用域就可以访问函数内部作用域的变量了；

简单的说，闭包就是有权限访问另一个函数内部作用域的变量的函数；

1. javascript 具有自动垃圾回收机制，函数运行完之后，其内部的变量和数据会被销毁；
2. 但是闭包就是在外部可以访问此函数内部作用域的变量，所以闭包的一个特点就是只要存在引用函数内部变量的可能，JavaScript 就需要在内存中保留这些变量。而且 JavaScript 运行时需要跟踪这个内部变量的所有外部引用，直到最后一个引用被解除（主动把外部引用赋为 null 或者页面关闭），JavaScript 的垃圾收集器才能释放相应的内存空间；这句话不是很好理解，下面用代码展示；

```js
function outer(){
    var a = 1;
    function inner(){
        return a++;
    }
    return inner;
}
var abc = outer();
//outer()只要执行过，就有了引用函数内部变量的可能，然后就会被保存在内存中；
//outer()如果没有执行过，由于作用域的关系，看不到内部作用域，更不会被保存在内存中了；

console.log(abc());//1
console.log(abc());//2
//因为a已经在内存中了，所以再次执行abc()的时候，是在第一次的基础上累加的

var def = outer();
console.log(def());//1
console.log(def());//2
//再次把outer()函数赋给一个新的变量def，相当于绑定了一个新的outer实例；

//console.log(a);//ReferenceError: a is not defined
//console.log(inner);//ReferenceError: a is not defined
//由于作用域的关系我们在外部还是无法直接访问内部作用域的变量名和函数名

abc = null;
//由于闭包占用内存空间，所以要谨慎使用闭包。尽量在使用完闭包后，及时解除引用，释放内存；
```

立即执行函数能配合闭包保存状态

```js
for(var i = 0; i < 3; i++){
    setTimeout(function(){
        console.log(i);    //3 3 3
        //在执行到这一行时，发现匿名函数里没有i，然后向往外部作用域找，然后找到的其实是for循环执行完了的i，也就是2++，3
    },0);
};

for(var i = 0; i < 3; i++){
    setTimeout((function(x){
        console.log(x);    //0 1 2
    })(i),0);
    //在立即执行函数内部i传给了x，并且锁在内存中，所以不会变
};
```

### 插件

用立即函数配合闭包写插件，防止变量全局污染，以及保证内部变量的安全；

```js
var Person = (function(){
    var _sayName = function(str){
        str = str || 'shane';
        return str;
    }
    var _sayAge = function(age){
        age = age || 18;
        return age;
    }

    return {
        SayName : _sayName,
        SayAge : _sayAge
    }
})();

//通过插件提供的API使用插件
console.log(Person.SayName('lucy')); //lucy
console.log(Person.SayName());//shane
console.log(Person.SayAge());//18
```

#### 一道经典面试题

下面的 ul 中，如何点击每一个 li 的时候弹出其下标?

```html
<ul>
  <li>index 00000</li>
  <li>index 11111</li>
  <li>index 22222</li>
</ul>
```

方法一： 闭包

```js
window.onload = function(){
    var oLi = document.getElementsByTagName('ul')[0].children;
    for (var i = 0; i < oLi.length; i++){

        // 第一种写法
        (function(index){
            oLi[index].onclick = function(){
                console.log(index);
            };
        })(i);

        // 第二种写法
        oLi[i].onclick = (function(index){
            return function(){
                console.log(index);
            }
        })(i);
    }
}
```

方法二： 将下标作为对象的一个属性，添加到每个数组元素中，（name: " i ", value: i 的值）;

```js
window.onload = function(){
    var oLi = document.getElementsByTagName('ul')[0].children;
    for (var i = 0; i < oLi.length; i++){
        oLi[i].i = i;
        oLi[i].onclick = function(){
            console.log(this.i);
        };
    }
}
```
