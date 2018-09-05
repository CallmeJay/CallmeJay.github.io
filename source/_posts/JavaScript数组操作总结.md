---
title: JavaScript数组操作总结
date: 2017-04-30 21:13:26
categories: JavaScript
tags: Array
---
### 数组的创建

数组的创建有两种方法，一种是通过字面量，另一种是通过Array构造函数。

#### 字面量

```js
var num1 = [1,2,3];
```

值得注意的是，利用字面量创建数组时，不可在最后一个数组元素添加“,”，因为在老版本的浏览器中，会出现bug。
<!--more-->

#### Array构造函数

```js
var num2 = new Array();//空数组
```

Array()可接收包含项作为参数，这时，会创建包含传入项的数组，如num3。
也可接收一个类型为number的数字作为参数，这时，会创建数组长度为该数字的数组，如num5。

```js
var num3 = new Array("1","2","3");//相当于["1","2","3"]
var num4 = new Array(3);//length为3，元素均为undefined的数组
var num5 = new Array(3).fill("1",0,2); //["1","1","1",undefined]
```

### 数组的转换

关于数组的转换，这里只提数组转换成字符串的两种方法。分别是toString()和join()。

#### toString()

该方法会返回数组各个项以逗号隔开的字符串。在使用alert(array)方法打印数组时，会隐性调用toString()方法。

```js
var num6 = [1,2,3];
var num6Str = num6.toString();//"1,2,3"
alert(num6);//"1,2,3"
```

#### join()

join()只接收一个参数，该参数为数组中各项拼接的字符串，若不传参，默认为用“,”作为分隔符，效果相当于toString()方法。

```js
var num7 = [1,2,3];
num7.join("$");//"1$2$3"
num7.join();//"1,2,3"
```

### 栈方法

栈是一种先进先出的数据结构，而数组提供的push()和pop()方法类似栈的行为，所以叫栈方法。

#### push()

push()方法可以接收多个参数，把它们依次添加至数组末尾，并返回修改后数组长度。

```js
var num8 = [1,2,3];
var num8Length = num8.push(4,5);
alert(num8);//1,2,3,4,5
alert(num8Length);//5
```

#### pop()

pop()方法是将数组的最后一项移除，并返回移除项。

```js
var num9 = [1,2,3];
var last = num9.pop();//3
```

### 队列方法

#### shift()

shift()方法是将数组的第一项移除，并返回移除项。

```js
var num10 = [1,2,3];
var first = num10.shift();//1
```

#### unshift()

unshift()方法与shift()方法用途相反，该方法可以接收多个参数，将参数依次加入数组的前端，并返回修改后数组的长度。

```js
var num11 = [1,2,3];
var addArray = num11.unshift("-1","0");//5
```

### 重排序方法

#### reverse()

reverse()方法是使数组中的项进行反转，并返回经过排序后的数组。

```js
var num12 = [1,2,3];
num12.reverse();//[3,2,1]
```

#### sort()

sort()方法是默认为按照ASCII码进行顺序排序。在使用sort()方法时，会先隐性调用toString()方法将数组中的每一项进行字符串转化，实际上是进行字符串ASCII进行排序，并返回排序后的数组。

对于数组项中均为number类型的数据，我们可以在sort()中传入一个比较函数，该比较函数接收两个参数，第一个参数为前一项，第二个参数为后一项。将两个参数进行对比，若return的值>0,则将两个对比项进行调换位置，调换位置的项，再与较前的一项进行对比，依次类推，直到return值返回为0或者<0为止。

在比较函数中，return值>0，数组项调换位置；return值<0，数组项不调换位置；return值=0，意味着两项相等，数组项不调换位置。

```js
var num13 = [3,2,7,14,111,0];
num13.sort(function(value1,value2){
    return value1-value2;
});
alert(num13);//0,2,3,7,14,111
```

### 操作方法

#### concat()

concat()方法是将数组与数组进行连结或者将数组项添加至数组末尾。

concat()会创建当前数组的一个副本，然后将接收到的参数依次添加到这个副本的末尾。如果没有传入参数，则返回的是当前数组的副本，相当于数组的复制；如果传入一个或多个数组参数，则会在副本的末尾依次添加数组参数中的每一个项；如果传入的是一个或多个非数组类型的数据，则这些值会依次被添加至副本的末尾。

*注：可利用concat()方法返回一个新数组的特性用于数组的复制。*

```js
var num14 = [1,2,3];
var num15 = num14.concat([4,5],[6,7]);
alert("参数为多个数组时："+num15);//1,2,3,4,5,6,7
var num16 = num14.concat(4,5);
alert("参数为非数组时："+num16);//1,2,3,4,5
```

#### slice()

slice()可以基于当前数组中的一个或多个项创建一个新的数组，并返回新的数组。

slice()可以接收一个或两个参数，即要返回项的起始位置和结束位置。当只有一个参数时，会返回从该参数指定的位置开始到数组的末尾位置；传入两个参数时，会返回从起始位置到结束位置前一项的数组项。

*注：可利用slice(0)方法返回一个新数组的特性用于数组的复制。*

```js
var num17 = [1,2,3];
num17.slice(1);//[2,3]
num17.slice(1,2);//[2]
```

#### splice()

splice()方法是比较强大的数组操作方法。因为可以利用该方法对数组进行删除，插入和替换操作。
`删除`：splice(index,number)，只需要传入两个参数，index为删除的第一项位置，number为删除的项数。
`插入`：splice(index,0,value1,value2,...)，需要至少传入三个参数，index为从位置index处插入value1,value2...。
`替换`：splice(index,number,value1,value2...)，需要至少传入三个参数，index为开始替换的位置，number为替换的项数，value1，value2等是替换的项。
`返回值`：splice()方法返回的始终是一个数组，若有删除项，则返回含有所有被删除项的数组，若没有删除项，这返回空数组。

```js
var num18 = [1,2,3];
//删除操作
num18.splice(0,2);//返回[1,2]，此时num18 = [3]
//插入操作
num18.splice(1,0,4,5,6);//返回[]，此时num18 =[3,4,5,6]
//替换操作
num18.splice(2,2,3,3);//返回[5,6]，此时num18 = [3,4,3,3]
```

### 位置方法

位置方法包含了两种方法，分别为indexOf()和lastIndexOf()，两个方法都接收两个参数，第一个参数为查找项，第二个参数（可选）为查找的起始位置，都返回查找项所出现的第一个位置，没有找到则返回-1。查找项和数组中的每一项进行比较时，使用使用的是全等操作符===，故需要值和数据类型一致。

#### indexOf()

indexOf(value)该方法是从数组的开头开始查找值为value的项。
indexOf(value,index)该方法是从下标为index的项开始查找值为value的项。

```js
var num19 = [1,2,3];
num19.indexOf(1);//返回0
num19.indexOf(3,1);//返回2
```

#### lastIndexOf()

lastIndexOf(value)该方法是从数组的末尾开始查找值为value的项。
lastIndexOf(value,index)该方法是从数组末尾开始往前（从0开始）index的项开始向数组开头查找值为value的项。

```js
var num20 = [1,2,3];
num20.lastIndexOf(1);//返回0
num20.lastIndexOf(1,1);//返回0
```

### 迭代方法

#### every()

every(function(item,index,array){reutrn...;})对数组的每一项都传入给定函数，如该函数对每一项都返回true，则返回true，否则为false。（类似且操作符）

```js
var num21 = [1,2,3];
var every = num21.every(function(item,index,array){
    return (item>0);
});//true
var every1 = num21.every(function(item,index,array){
    return (item>2)
});//false
```

#### some()

some(function(item,index,array){return...})对数组的每一项都传入给定函数，如函数对某一项返回true，则返回true，否则为false。（类似或操作符）

```js
var num22 = [1,2,3];
var some= num21.some(function(item,index,array){
    return (item>2);
});//true
var some1= num22.some(function(item,index,array){
    return (item>6)
});//false
```

#### forEach()

forEach(function(item,index,array){})对数组的每一项都传入给定函数，没有返回值。（类似for循环）

```js
var num23 = [1,2,3];
num23.forEach(function(item,index,array){
    console.log(item);
});
```

#### map()

map(function(item,index,array){})对数组的每一项都传入给定函数，返回值为对数组操作后的数组。

```js
var num24 = [1,2,3];
num24.map(function(item,index,array){
    return item+2;
});//[3,4,5]
```

#### filter()

filter(function(item,index,array){})对数组的每一项都传入给定函数，返回值为给定函数返回为true的项组成的数组。

```js
var num25 = [1,2,3];
num25.filter(function(item,index,array){
    return (item>2);
});//[3]
```

### 归并方法

归并方法有两个，分别为reduce()和reduceRight()。该两个方法接收两个参数，分别为给定函数和pre初始值（可选）。均迭代数组的每一项传入给定函数，并构造一个最终返回值。

#### reduce()

reduce(function(pre,cur,index,array){return...})从第一项开始，迭代时，会将给定函数返回的值作为pre参数给到下一次迭代，直到最后。cur指当前项，index为当前项下标，array为调用的数组。

```js
var num26 = [1,2,3];
//只传给定函数
num26.reduce(function(pre,cur,index,array){
    return pre+cur;
});//6
//传入给定函数和pre初始值
num26.reduce(function(pre,cur,index,array){
    return pre+cur;
},4);//10
```

#### reduceRight()

reduceRight()和reduce()方法除了从数组的末尾还是开始开始遍历不同外，其他都一致。reduceRight()是从数组末尾开始遍历。