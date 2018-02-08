---
title: 剖析let/const在for循环中的作用原理
date: 2017-07-01 20:26:38
categories: JavaScript
tags: ES6
---
1、先来看一个例子
```js
var a=[];  
for(var i = 0;i<3;i++){   
   a[i]=function(){console.log(i)}  
}
a[1](); //3
a[2](); //3
```
<!--more-->
2、再来看一下For循环的过程
```js
for (语句 1; 语句 2; 语句 3)
{
  被执行的代码块
}
// 语句 1 在循环（代码块）开始前执行
// 语句 2 定义运行循环（代码块）的条件
// 语句 3 在循环（代码块）已被执行之后执行
```
3、例子for循环执行过程伪代码如下：
```js
var a=[]
var i = 0;

if(i<3) {
  a[i]=function(){console.log(i)}  
}
i++;

if(i<3) {
  a[i]=function(){console.log(i)}  
}
i++;

if(i<3) {
  a[i]=function(){console.log(i)}  
}
i++;

/* 数组a此时的状态
a = [
  function(){console.log(i)},
  function(){console.log(i)},
  function(){console.log(i)}
]
*/

// a[1] = function(){console.log(i)}

a[1](); // 打印3，因为i现在的值是3

注：“i现在的值是3”这句话在某种意义上是不准确的，
从作用域链的角度来说a[1]在它的if{}块作用域内没有找到i，
要去上一级作用域局部作用域中找，这时候找到了i，
而局部作用域中i现在的值是3。
```
4、如果把for循环的var换成let
```js
var a=[];  
for(let i = 0;i<3;i++){   
   a[i]=function(){console.log(i)}  
}
a[1](); //1

// 执行过程
var a=[]
let i = 0;

if(i<3) {
    let i = i;
    a[i]=function(){console.log(i)}  
}
i++;

if(i<3) {
      let i = i;
    a[i]=function(){console.log(i)}  
}
i++;

if(i<3) {
      let i = i;
    a[i]=function(){console.log(i)}   
}
i++;

// console.log(i)在其块级作用域内就找到了i，
每次for循环把当前i值存入块级作用域。
所以a[1]()会打印1；
```

