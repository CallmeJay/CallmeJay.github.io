---
title: Chrome控制台console用法
date: 2017-06-07 22:55:38
categories: JavaScript
tags: console
---
下面我们来看看console里面具体提供了哪些方法可以供我们平时调试时使用。
`console.dir(console);`可以看到有很多方法，下面主要介绍常用的几种方法。
##　输出信息的四种方法
```js
console.log();// 用于输出普通信息
console.info();// 用于输出提示性信息（蓝色叹号）
console.error();// 用于输出错误信息（红色X号）
console.warn();// 用于输出警告信息（黄色叹号）
```
<!--more-->
## console.count() 统计
```js
function fn(){
    // blah blah....
    console.count("fn执行次数：");
}
fn();  // fn执行次数：1
fn();  // fn执行次数：2
fn();  // fn执行次数：3
```
## console.dir() 输出DOM树 查看对象方法
```js
var myObject = {
    a:"1",
    b:2,
    c:true,
    myFunc: function(){
        alert("Hello world");
    }
};
console.dir(myObject);
```
## console.time 计时
```js
// console.time()搭配console.timeEnd()一起使用;
// 使用console.time统计实例化10000个对象所用时间
console.time("Array initalize");
var arr = new Array(10000);
for(var i = arr.length -1;i>= 0;i--){
    arr[i] = new Object();
}
console.timeEnd("Array initalize");
```
## keys和values
keys和values 前者返回传入对象所有属性名组成的数据，后者返回所有属性值组成的数组。
```js
var myObj = {"name": "xiaoming","age": 24,"sex":"male"};
keys(myObj); //["name","age","sex"]
values(myObj); //["xiaoming",24,"male"]
```
## 支持类jQuery选择器
Chrome 控制台中原生支持类jQuery的选择器，也就是说你可以用$加上熟悉的css选择器来选择DOM节。如输入$('body')查看对应DOM树。