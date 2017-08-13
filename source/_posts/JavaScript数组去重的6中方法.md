---
title: JavaScript数组去重的6中方法
date: 2017-05-17 21:17:57
categories: JavaScript
tags: JavaScript
---
### 题目说明:
#### 题目
```js
声明一个数组,将数组中的相同元素过滤掉,然后存入到一个新数组resultArr = []中;
var arr = [1, 2, 3, 1, 3, 4, 5, 5];
var resultArr = []//该数组的结果应该为1,2,3,4,5
```
<!--more-->
#### 基本思路:
1.采用循环嵌套
2.外层循环将数组中的值赋给新的数组
3.内层循环通过if语句判断新数组中是否有该值,如果有,则在外层循环中通过if语句判断,让元素不赋给新数组.
### 方法解析
#### 比较内层循环变量的值.
```js
var arr = [1, 2, 3, 1, 3, 4, 5, 5];
var resultArr = [];
//外层循环进行赋值
for (i = 0; i < arr.length; i++) {
    //内层循环进行重复判断
    for (j = 0; j < resultArr.length; j++) {
        if (resultArr[j] == arr[i]) {
            break;//一旦有重复arr[i],跳出循环
        }
    }
    //如果resultArr这个数组中没有arr[i],那么j经过内层循环后没有break,值应为resultArr.length
    if (j == resultArr.length) {
        resultArr[resultArr.length] = arr[i];
    }
}
console.log(resultArr); //1,2,3,4,5
```
#### 计数法.
```js
var arr = [1, 2, 3, 1, 3, 4, 5, 5];
var count; //声明一个变量,用来计数
var resultArr = [];
for (i = 0; i < arr.length; i++) {
    count = 0;  //每次初始化计数变量
    for (j = 0; j < resultArr.length; j++) {
        if (resultArr[j] == arr[i]) {
            count++; //一旦进入该if语句,则计数变量自加
            break;
        }
    }
    //如果count等于0 ,那么说明内层循环中没有进入if语句,则resultArr中没有和arr[i]重复的元素
    if (count == 0) {
        resultArr[resultArr.length] = arr[i];
    }
}
console.log(resultArr); //1,2,3,4,5
```
#### flag标志法(也叫假设成立法)
```js
var arr = [1, 2, 3, 1, 2, 3, 4, 5, 5];
var resultArr = []; //[1,2,3]
var flag; //声明一个变量,用来当做假设变量
for (var i = 0; i < arr.length; i++) {
    flag = true;  //每次初始化假设变量,假设result中没有和arr[i]重复的元素,赋值true,
    for (j = 0; j < resultArr.length; j++) {
        if (resultArr[j] == arr[i]) {
            flag = false; //一旦进入该if语句,则表示resultArr中有重复的的arr[i],则假设不成立,赋值false
            break;
        }
    }
    //如果flag为ture,赋值给resultArr这个数组
    if (flag) {
        resultArr[resultArr.length] = arr[i];
    }
}
console.log(resultArr);//1,2,3,4,5
```
#### 使用sort()方法排序后比较
```js
var arr = [1, 2, 3, 1, 2, 3, 4, 5, 5];
var resultArr = [];
//使用数组的sort()方法对arr进行排序,也可以使用冒泡排序
arr.sort(function (a, b) {
    return a - b;
});
//排序后,arr变成了[1, 1, 2, 2, 3, 3, 4, 5, 5]

//使用for循环,从上面的数组可以看出,如果数组元素arr[i]和arr[i+1]不相等,则表示arr[i]之后不再有与arr[i]相等的重复元素
for (i = 0; i < arr.length; i++) {
    if (arr[i] != arr[i + 1]) {
        //将arr[i]赋值给resultArr数组.
        resultArr[resultArr.length] = arr[i];
    }
}
console.log(resultArr); //1,2,3,4,5
```
#### 使用filter()方法比较
```js
var arr = [1, 2, 3, 1, 2, 3, 4, 5, 5];
var resultArr;
//采用filter()方法过滤掉数组中重复的元素,filter()方法中传入一个过滤函数作为参数.
resultArr = arr.filter(function (item, index, self) {
    //indexOf返回的是arr中的第一个元素的索引值,所以下面语句过滤掉了arr中重复的元素.
    return self.indexOf(item) == index;

});
console.log(resultArr); //1,2,3,4,5
```
#### 使用缓存对象方法比较
```js
var arr = [1, 2, 3, 1, 2, 3, 4, 5, 5];
var resultArr = [];
var cache = {};  //声明一个缓存对象
for(var i = 0 ; i < arr.length; i ++){//循环数组arr
    if(!cache[arr[i]]){//进行判断,如果缓存对象中没有这个属性名的话,我们就把该数组赋值给resultArr,并把该属性名赋值为true
        cache[arr[i]] = true;
        resultArr.push(arr[i]);
    }
}
console.log(resultArr);
```