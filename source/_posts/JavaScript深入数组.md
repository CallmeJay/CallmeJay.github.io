---
title: JavaScript深入数组
date: 2018-02-26 23:13:39
categories: JavaScript
tags: Array
---
### 数组基础
#### 简介
数组是应用最广泛的数据存储结构。它被植入到大部分编程语言中。在 ECMAScript 中数组是非常常用的引用类型。
<!--more-->
![数组](http://oonulpk6h.bkt.clouddn.com/Array.png)
#### 属性
**length**
length属性表示数组的长度，即其中元素的个数。
JavaScript数组的length属性时可变的，当length属性被设置的更大时，整个数组的状态事实上不会发生变化，仅仅是length属性变大;当length属性被设置的比原来小时，则原先数组中索引大于或等于length的元素的值全部被丢失。

**prptotype**
返回对象类型原型的引用。prototype属性是object共有的。
一般用来给数组实例添加方法。

**constructor**
表示创建对象的函数。
说明：constructor属性是所有具有prototype的对象的成员。constructor属性保存了对构造特定对象实例的函数的引用。

#### 基本操作
##### 创建数组
```js
// 数组实例的创建
var arr = [];
var arr = new Array();
var arr = new Array(5); // 创建一个length为5的数组 [undefined, undefined, undefined, undefined, undefined]
var arr = new Array(1,2,3); // 创建数组并赋值 [1,2,3]
var arr = new Array(3).fill(5); // 创建一个length为3的数组，并里面赋值均为5 [5,5,5] 
var arr = Array.of(7); // 创建数组并赋值[7]
var arr = Array.of(1,2,4); // 创建数组并赋值 [1,2,4]
```
##### 检测数组
```js
// 判断一个对象是不是一个数组
var arr = [];
if(arr instanceof Array) {} // 方法1

if(Object.prototype.toString.call(arr) == '[object Array]') {}// 方法2

if(Array.isArray(arr)) {} // 方法3

if(arr.constructor == Array){} // 方法4
```
#### 数组方法 
**Array.isArray(obj)**
检测对象是否Array,是则范湖true,否则为false.
**Array.from(arrayLike,mapFn,thisArg)**
该方法从一个类似数组或可迭代对象创建一个新的数组实例。参数 arrayLike 是想要转换成真实数组的类数组对象或可遍历对象。mapFn 是可选参数，如果指定了该参数，则最后生成的数组会经过该函数的加工处理后再返回。thisArg是可选参数，为执行 mapFn 函数时 this 的值。

所谓类似数组的对象，本质特征只有一点，即必须有length属性。因此，**任何有length属性的对象，都可以通过Array.from方法转为数组。**

实际应用中，常见的类似数组的对象是 DOM 操作返回的 NodeList 集合，以及函数内部的 arguments 对象。
```js
var arrayLike = {
    0: 'a',
    1: 'b',
    2: 'c',
    length: 3
};
let arrayLike2 = {length:3};
let str = 'abcd';

let newArray = Array.from(arrayLike); // ['a','b','c']
let newArray2 = Array.from(arrayLike, (i) => { i+ '1'}); // ['a1','b1','c1']
let newArray3= Array.from(arrayLike2); // [undefined,undefined,undefined]
let newArray4= Array.from(str); // ['a','b','c','d']
```
**Array.of(item…)**
该方法用于创建数组实例。该方法用于替代 Array() 或 new Array()。Array.of() 和 Array 构造函数之间的区别在于处理整数参数：Array.of(7) 创建一个具有单个元素 7 的数组，而 Array(7) 创建一个包含 7 个 undefined 元素的数组。
```js
Array.of(7);       // [7] 
Array.of(1, 2, 3); // [1, 2, 3]
Array(7);          // [ , , , , , , ]
Array(1, 2, 3);    // [1, 2, 3]
```
#### 数组实例方法
##### 转换方法
**arr.join(separator)**
把数组构构造成字符串，它先把数组中的每个元素转换成字符串，然后再用 separator 分隔符把它们链接在一起，separator 分隔符默认是逗号 “,”，要想做到无间隔链接，可以使用空字符串作为 separator：
```js
var arr = [1,2,3,4,5]
arr.join('|') //"1|2|3|4|5"
arr.join("") //12345
//另所有对象有具有的 toLocaleString、toString、valueOf，可以看作是join的特殊用法，不常用
```
##### 栈方法
**arr.push(item…)**
将一个或多个新元素添加到数组结尾，并返回数组新长度。
**arr.pop()**
移除最后一个元素并返回该元素值。
##### 队列方法
**arr.unshift(item...)**
将一个或多个新元素添加到数组开始，数组中的元素自动后移，返回数组新长度。
**arr.shift()**
移除第一个元素并返回该元素值，数组中元素自动前移.如果这个数组是空的，它会返回 undefined。shift 通常比 pop 慢的多。
##### 重排序方法
**arr.reverse()**
反转数组的顺序。
**arr.sort(compareFn)**
给数组排序，默认升序。

注意： sort 默认会将数组内容视为字符串来排序，所以对数字排序时默认的排序规则会错的离谱。一般我们给sort带入个比较函数来替代原来的默认的比较方法，比较方法接受两个参数，如果两个参数相等则返回0，如果第一个参数应该排在前面则返回一个负数，如果第二个参数应该排在前面则返回一个正数：
```js
//数组排序
var arr = [2,3,1,5,4]
arr.sort(function(a, b) {
return a - b
}) // [1,2,3,4,5]
```
##### 操作方法
**arr.concat(item…)**
该方法产生一份 arr 的浅复制，并将多个数组（也可以是字符串，或者是数组和字符串的混合）附加在其后连接为一个数组，返回连接好的新的数组。

**arr.slice(start, end)**
该方法对数组中的一段做浅复制，首先复制数组 arr[start] 至 arr[end] 的部分，注意不包括 end 对应的元素，如果省略 end 将复制 start 之后的所有元素（或者理解成 end 的默认值为 arr.length）。字符串也有个同名方法 string.slice。

**arr.splice(start, deleteCount, item…)**
该方法从 arr 中移除一个或多个元素，并将新的 item 插入至移除元素的开始位置， 参数 start 是移除元素的开始位置，deleteCount 是要移除的元素的个数，item 是要被插入的元素。它返回一个包含被移除元素的数组。

**arr.copyWithin(target, start, end)**
该方法复制数组的一部分到同一数组中的另一个位置（会覆盖原成员），并返回修改后的数组。使用这个方法，会修改当前数组。参数 target 为开始替换数据的位置，若 target 大于等于 arr.length，将会不发生拷贝。start 是可选参数，为开始读取数据的位置，默认为0。end 是可选参数，为停止读取数据的位置，默认为 arr.length。
```js
[1, 2, 3, 4, 5].copyWithin(0, 3)
// [4, 5, 3, 4, 5]
```
上面代码表示将从3号位直到数组结束的成员（4和5），复制到从0号位开始的位置，结果覆盖了原来的1和2。

**arr.fill(value, start, end)**
该方法使用给定值填充一个数组，参数 value 是用来填充数组的值。start 是可选参数，为填充开始位置，默认为 0。end 是可选参数，为填充的结束位置，默认为 arr.length。
```js
[1, 2, 3].fill(4)            // [4, 4, 4]
[1, 2, 3].fill(4, 1 , 2)            // [1, 4, 3]
``` 
fill 方法是个可变方法, 它会改变调用它的 this 对象本身, 然后返回它, 而并不是返回一个副本。

##### 位置方法
**arr.indexOf(searchElement,start)**
该方法返回要查找的项在数组中的位置，如果没找到返回 -1。接受两个参数，searchElement 是要查找的项，start 是查找起始位置的索引，默认是0。

**arr.lastIndexOf(searchElement, start)**
从 start 位置开始向前查找，start 默认值为 arr.length – 1。

注意该方法在比较查找项与数组中每一项时，会使用全等操作符，也就是要求查找的项必须严格相等。

**arr.includes(searchElement, fromIndex)**
该方法用来判断当前数组是否包含某指定的值，如果是，则返回 true，否则返回 false。参数 searchElement 为需要查找的元素值。参数 fromIndex 是可选参数，从该索引处开始查找 searchElement，如果为负值，则按升序从 array.length + fromIndex 的索引开始搜索，默认为 0。 如果fromIndex 大于等于数组长度 ，则返回 false 。该数组不会被搜索。
该方法属于ES7，但Babel转码器已经支持。
```js
[1, 2, 3].includes(2);     // true
[1, 2, 3].includes(4);     // false
[1, 2, NaN].includes(NaN); // true
```
没有该方法之前，我们通常使用数组的indexOf方法，检查是否包含某个值。
```js
if (arr.indexOf(el) !== -1) {
  // ...
}
```
indexOf方法有两个缺点，一是不够语义化，它的含义是找到参数值的第一个出现位置，所以要去比较是否不等于-1，表达起来不够直观。二是，它内部使用严格相当运算符（===）进行判断，这会导致对NaN的误判。includes使用的是不一样的判断算法，就没有这个问题。
```js
[NaN].indexOf(NaN)
// -1
[NaN].includes(NaN)
// true
```
includes() 方法有意设计为通用方法。它不要求this值是数组对象，所以它可以被用于其他类型的对象 (比如类数组对象)。
```js
(function(){
console.log([].includes.call(arguments, 2)) // true
console.log([].includes.call(arguments, 4)) // false
})(1,2,3)
```
##### 迭代方法
**arr.every(callback,thisArg)**
对数组中的每一项运行给定函数，如果该函数对每一项都返回 true，则返回 true。callback 被调用时传入三个参数：元素值，元素的索引，原数组。thisArg 为可选参数，指定执行 callback 时使用的 this 值。

**arr.some(callback,thisArg)**
对数组中的每一项运行给定函数，如果该函数对任意一项返回true,则返回true。

**arr.filter(callback,thisArg)**
对数组中的每一项运行给定函数，返回该函数会返回true的项组成的数组。

**arr.forEach(callback)**
对数组中的每一项运行给定函数，这个函数没有返回值。本质上和使用for一样。

**arr.map(callback)**
对数组中的每一项运行给定的函数，返回每次函数调用后结果组成的数组。

**arr.find(callback,thisArg)**
该方法对数组所有成员依次执行callback函数，直到找出第一个返回值为true的成员并返回。如果没有，则返回undefined.
```js
[1,2,-5,19].find((v,i,arr) => v < 0) // -5
```
**arr.findIndex(callback, thisArg)**
该方法与 arr.find() 类似，对数组中的成员依次执行 callback 函数，直至找到第一个返回值为 true 的成员，然后返回该成员的索引。如果没有符合条件的成员，则返回 -1。
```js
[1, 5, 10, 15].findIndex((v, i , arr)=>  v > 9) // 2
```

**arr.entries()**
**arr.keys()**
**arr.values()**
这三个方法都返回一个新的Array iterator对象，可以用`for...of`循环进行遍历，区别是`keys()`是对键名的遍历、`values()`是对键值的遍历，`entries()`是对键值对的遍历。
```js
for (let index of ['a', 'b'].keys()) { console.log(index); }
// 0
// 1
 
for (let elem of ['a', 'b'].values()) { console.log(elem) ;}
// 'a'
// 'b'
 
for (let [index, elem] of ['a', 'b'].entries()) { console.log(index, elem);}
// 0 "a"
// 1 "b"
 
var arr = ["a", "b";
var iterator = arr.entries(); // undefined
 
console.log(iterator); // Array Iterator {}
 
console.log(iterator.next().value);  // [0, "a"]
console.log(iterator.next().value);  // [1, "b"]
```
##### 归并方法
**arr.reduce(callback,initialValue)**
**arr.reduceRight(callback,initialValue)**
这个两个方法都会迭代数组所有的项，然后返回一个最终值。reduce()方法从数组的第一项开始，逐个遍历到最后，而reduceRight()则从数组的最后一项开始，向前遍历到第一项。它们接受两个参数，callback 每一项上调用的函数，callback 被调用时传入四个参数：上一次调用回调返回的值、正在处理的元素、正在处理的元素的索引值（如果提供了 initialValue ，从0开始；否则从1开始）、原数组。initialValue 是可选项，作为归并基础的初始值，其值用于第一次调用 callback 的第一个参数。

### 数组常见问题练习
#### 数组合并
##### 多个一维数组合并
如有两个一维数组，要将他们合并成一个数组:
```js
var arr1 = [1, 2]
var arr2 = [3, 4]
 
// => [1,2,3,4]
```
Array.prototype.concat()方法
```js
var arr = arr1.concat(arr2) //[1, 2, 3, 4]
```
for循环和Array.prototype.push()
```js
function flation(arr1, arr2) {
    for (var i = 0; i < arr2.length; i++) {
        arr1.push(arr2[i])
    }
    return arr1
}
```
reduce()或reduceRight()方法
```js
function flatten (arr1,arr2){
    arr2.reduce(function(prev,curr){
        return prev.push(curr);
    },arr1)
}
```
##### 多维（复合）数组合并成一维数组
例如有这么一组多维数组，要把它拍平成1维数组：
```js
var myArray = [[1, 2], [3, 4, 5], [6, 7, 8, 9], [11,12,[12,13,[14]]], 10, 11];
 
// => [1, 2, 3, 4, 5, 6, 7, 8, 9, 11, 12, 12, 13, 14, 10, 11]
```
Array.prototype.push()方法
```js
unction flatten(arr, result) {
    if (!result) {
        result = []
    }
    for (var i = 0; i < arr.length; i++) {
        if(arr[i].constructor == Array) {
            flatten(arr[i], r)
        } else {
            result.push(arr[i])
        }
    }
    return result
}
flatten([[1, 2], [3, 4, 5], [6, 7, 8, 9], [11,12,[12,13,[14]]], 10, 11])
//[1, 2, 3, 4, 5, 6, 7, 8, 9, 11, 12, 12, 13, 14, 10, 11]
```
Array.prototype.concat()方法
```js
//省略对二维以上数组的判断
function flatten(arr) {
    var result = []
    for (var i = 0; i < arr.length; i++) {
        result = result.concat(arr[i])
    }
    return result
}
flatten([[1, 2],[3, 4, 5], [6, 7, 8, 9]]);//[1, 2, 3, 4, 5, 6, 7, 8, 9] 
flatten([[1, 2],[3, 4, 5], [6, 7, 8, 9],10,11]);//[1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11]
```
可以通过Function.prototype.apply()方法，让其变得更简单:
```js
//省略对二维以上数组的判断
function flatten(arr) {
    return Array.prototype.concat.apply([], arr)
}
```
在 ES6 中，还可以这样写：
```js
//省略对二维以上数组的判断
function flatten(arr) {
    return [].concat(...arr)
}
```
如果要处理三维或更多维数组，则需要加一些判断：
```js
function flatten(arr) {
    arr = [].concat.apply([], arr)
    return arr.some(Array.isArray) ? flatten(arr) : arr
}
flatten([[1, 2], [3, 4, 5], [6, 7, 8, 9], [11,12,[12,13,[14]]], 10, 11])
//[1, 2, 3, 4, 5, 6, 7, 8, 9, 11, 12, 12, 13, 14, 10, 11]
```
使用join()和split()
利用数组实例的join()方法和字符串实例的split()方法，也可以将多维数组拍平：
```js
function flatten(arr) {
    return arr.join(',').split(',')
}
flatten([[1, 2], [3, 4, 5], [6, 7, 8, 9], [11,12,[12,13,[14]]], 10, 11])
//["1", "2", "3", "4", "5", "6", "7", "8", "9", "11", "12", "12", "13", "14", "10", "11"]
```
这种方法的缺点在于返回的数组内全是字符串，如果不需要校验内容类型的话，可以使用这种方法。
#### 数组去重
##### 双重循环去重
思路：
* 构建一个空数组来存放去重后的数组
* 对原数组做循环遍历，每次从数组中取出一个元素与结果数组做对比
* 如果原数组取出的元素与结果数组元素相同，则跳出循环；反之则将其存放到结果数组。

```js
function unique (arr) {
    let result = [];
    if(arr.length<2) return;
    result[0] = arr[0];
    for(let i = 1;i<arr.length;i++){
        var repeat = false;
        for(let j= 0;j<result.length;j++) {
            if(arr[i] == result[j]) {
                repeat = true;
                break;
            }
        }
        if(!repeat) {
            result.push(arr[i]);
        }
    }
    return result;
}
unique([1, 2, 3, 4, 3, 2, 'a', 'b', 'a']);
//[1, 2, 3, 4, "a", "b"]
```
这种方法也可以采用forEach()方法和indexOf()方法模拟实现：
```js
function unique (arr) {
    let result = [];
    if(arr.length<2) return;
    result[0] = arr[0];
    arr.forEach((item) => {
        if(!result.includes(item)) result.push(item);
    })
    return result;
}
```
##### 排序遍历去重
思路：
* 构建一个空数组来存放去重后的数组
* 用`sort()`方法对原数组做一个排序，排完序后对数组做遍历，检测数组中第i个元素与结果数组中的最后一个元素是否相同，如果不同，则放到结果数组中。

```js
function unique () {
    arr.sort();
    var result = [];
    arr.forEach( item => {
        if(item !== result[result.length -1]) {
            result.push(item);
        }
    })
    return result;
}
unique([1, 2, 3, 4, 3, 2, 'a', 'b', 'a']);
```
这种方法有两个特色：
* 去重后的数组会做排序，主要是因为原数在去重前做了排序
* 去重后的数组，与数字相同的数字字符无法区分，比如‘1’和1

##### 对象键值对法
思路：
* 创建一个js对象及新数组
* 遍历原数组，每次取出一个元素与js对象的键做对比
* 如果不包含，将存入对象的元素的值推入到结果数组中，并将object对象中该属性名的值设为1

```js
function unique(arr) {
    var result = []
    var object = {}
    for (var i = 0; i < arr.length; i++) {
        if (!object[typeof(arr[i]) + arr[i]]) {  //键名里加入typeof(arr[i])是为了区别不同类型的值，如1和`1`
            result.push(arr[i])
            object[typeof(arr[i]) + arr[i]] = 1
        }
    }
    return result
}
unique([1, 2, 3, 4, 3, 2, '1', 'a', 'b', 'a']);
//[1, 2, 3, 4, "a", "b"]
```
这种方法比较耗内存，但运行下来耗时最少，是较为优秀的方案。
##### 使用ES6的Set和Map方法
```js
// 借助Map数据结构
function unique (arr) {
    const seen = new Map();
    return arr.filter((item) => !seen.has(item) && seen.set(item,1));
}

//借助 Set 数据结构
function unique(arr) {
    return Array.from(new Set(arr)) //或 return [...new Set(arr)]
}
 
unique([1, 2, 3, 4, 3, 2, '1', 'a', 'b', 'a']);
//[1, 2, 3, 4, "1", "a", "b"]
```
#### 数组随机排序
##### 递归的方法
思路:
不断从原数组中随机取一个元素放进新数组，同时删除原数组中该值，递归重复至全部取出。
```js
function randomSort(arr,newArr = []) {
    // 如果原数组只有一个值
    if(arr.length == 1) {
        newArr.push(arr[0]);
        return newArr; // 递归结束
    };

    let random = Math.ceil(Math.random()*arr.length-1);
    newArr.push(arr[random]);
    arr.splice(random,1);
    return randomSort(arr,newArr);
}
randomSort([1, 2, 3, 4, 5, 6, 7]); //[2, 3, 1, 5, 6, 7, 4]
randomSort([1, 2, 3, 4, 5, 6, 7]); //[3, 4, 2, 5, 1, 6, 7]
```
##### 随机交换数组内的元素 (原理from underscore.js）
思路：
遍历数组，每次从i位置后元素随机挑一个放到i位置，将原i位置元素放至被挑元素位置
```js
Array.prototype.shuffle = function() {
    var len = this.length,
        arr = this.slice(0),
        temp, index;
    for (var i = 0; i < len; i++) {
        index = i + Math.floor(Math.random() * (len - i))
        [arr[i],arr[index]] = [arr[index],arr[i]];
    }
    return arr.slice(0) 
}
```
##### 随机从原数组抽取一个元素,加入到新数组
思路：
遍历数组，每次从数组中随机挑一个元素（随机数最大值为原数组剩余长度），将该元素拿出来放入新数组。
```js
Array.prototype.shuffle = function() {
    var len = this.length,
        arr = this.slice(0),
        result = [],
        index;
    for (var i = 0; i < len; i++) {
        index = Math.floor(Math.random() * (len - i))
        result.push(arr.splice(index, 1)[0])
    }
    return result;1
}
[1, 2, 3, 4, 5, 6, 7].shuffle()
//[5, 3, 2, 1, 7, 4, 6]

