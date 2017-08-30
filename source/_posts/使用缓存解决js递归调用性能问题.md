---
title: 使用缓存解决js递归调用性能问题
date: 2017-05-20 10:43:18
categories: JavaScript
tags: 递归
---
### 问题描述
斐波那契序列通常是用来解释递归调用。
```js
function fibonacci(n) {
    if(n==0 || n == 1)
        return n;
    return fibonacci(n-1) + fibonacci(n-2);
}
```
这个算法以教学为目的,但非常低效的,不仅因为递归,而且两次调用fibonacci函数,在函数里面右侧调用的fibonacci(n-2) 在表达式左侧调用fibonacci(n-1)时就已完全计算过一遍。
<!--more-->

这个算法效率是如此之低,斐波纳契数超过50的实在太多了。你可以去喝杯咖啡或去睡午觉时等待答案。但如果你就用这个代码在codewars上很可能得到一个超时错误。

对于这个特定卡塔(类似打怪升级里面的级数)，我们想实现缓存的解决方案。这是特别酷的,因为它将让我们继续使用递归算法,同时仍然保持足够迅速的得到一个答案。

memoize的版本的诀窍是,保持一个缓存数据结构(最有可能的关联数组),将斐波纳契数列的值缓存。当获取一个斐波那契数列值时,首先在缓存中查找，如果有则直接返回值,如果没有,再计算并把它放进缓存。

使用memoize的数据结构重构函数的递归Fibonacci以避免递归调用的缺陷。
### 分析
斐波那契数列里面不断的递归调用自身，列入输入的是 70，那么需要计算69和68的值。
在计算69的过程中又计算了 68、67、、、、、1。 计算 68的过程又计算了 67、66、、、、、、、1的值，如此重复计算的值太多了，花费的时间也就比较多。

缓存思想恰好可以减少不必要的重复计算。当第一遍计算69的值时就递归计算了 68、67、66、、、1的值，之后的每次都先查看是否有缓存，有就直接返回缓存值，避免了重复计算。
### 代码
```js
let cache = {};
let fibonacci = function(n) {
    if(n==0 || n == 1)
        return n;
    if(cache[n]){
      return cache[n];
    }
    
    return cache[n] = fibonacci(n-1) + fibonacci(n-2);
}
```
### 性能测试
```js
//没有缓存时
let tesetNum = 40;
console.time('NoCache');
function fibonacci1(n) {
    if(n==0 || n == 1)
        return n;
    return fibonacci1(n-1) + fibonacci1(n-2);
}
fibonacci1(tesetNum);
console.timeEnd('NoCache');

// 使用缓存时
console.time("HasCache");
let cache = {};
let fibonacci = function(n) {
    if(n==0 || n == 1)
        return n;
    if(cache[n]){
      return cache[n];
    }
    
    return cache[n] = fibonacci(n-1) + fibonacci(n-2);
}
fibonacci(tesetNum);
console.timeEnd('HasCache');

// 输出
// NoCache: 1717.834ms
// HasCache: 0.159ms
```
通过性能测试可以看到，当测试数是40时不适用缓存消耗的时间就是使用缓存的1700多倍(好可怕的数据)，我试了下当测试数据是300时，，，，，，，，我就等不急它的执行了。
### 使用场景
当递归调用里有大量重复计算的情景，或者组件、数据等重复加载的情况下，使用缓存是个不错的选择(典型的以空间换时间)
