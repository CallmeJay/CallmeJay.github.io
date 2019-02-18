---
title: 经典前端面试题(JavaScript)
date: 2017-07-12 21:01:02
categories: JavaScript
tags: 面试题
---

#### 写出简单描述 html 标签（不带属性的开始标签和结束标签）的正则表达式，并将以下字符串中的 html 标签去除掉

```js
var reg = /(<\w+>)|(<\/\w+>)/gi;
var str = "<div>这里是div<p>里面的段落</p></div>";
alert(str.replace(reg, ""));
```

<!--more-->

#### 编写一个函数,用于统计一个字符串中出现次数最多的字符和其出现的次数？

```js
var str = "asdfssaaasasasasaa";
function findMaxAppearChar() {
  var arr = str.split("");
  var obj = {};
  var maxKey = "";
  var maxIndex = 0;
  for (var i = 0; i < arr.length; i++) {
    if (obj[arr[i]]) {
      obj[arr[i]] += 1;
    } else {
      obj[arr[i]] = 1;
    }
  }
  for (key in obj) {
    if (obj[key] > maxIndex) {
      maxKey = key;
      maxIndex = obj[key];
    }
  }
  return "出现最多字符是" + maxKey + ",一共出现了" + maxIndex + "次";
}
```

#### 闭包是什么? 有什么特性? 对页面有什么影响?

(1)闭包是什么:闭包是指有权访问另一个函数作用域中变量的函数.
(2)闭包有什么特性:
通过闭包,可以提供私有作用域.
函数执行完后,内部不会释放
通过闭包,可以实现带有存储函数运行时变量的函数.
(3)对页面有什么影响:大量使用闭包会造成网页的性能问题，在 IE 中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。

#### 写一个 reverseStr 函数来实现字符串反转，如将 '12345678' 变成 '87654321'

```js
var str = "12345678";
function reverseStr(str) {
  return str
    .split("")
    .reverse()
    .join("");
}
```

#### 将数字 12345678 转化成 RMB 形式 如： 12,345,678

```js
function formatNumberToRMB(number) {
  var str = number + "";
  var arr = str.split("").reverse(); //[8,7,6,5,4,3,2,1]
  var resultArr = [];
  for (var i = 0; i < arr.length; i++) {
    resultArr.push(arr[i]);
    if ((i + 1) % 3 == 0 && i + 1 != arr.length) {
      resultArr.push(",");
    }
  }
  return resultArr.reverse().join("");
}
```

#### 请写一个阻止冒泡的兼容函数

```js
function stopPropagation(e) {
  e = window.event ? window.event : e;
  if (e.stopPropagation) {
    e.stopPropagation();
  } else {
    e.cancelBubble = true;
  }
}
```

#### 用 js 实现随机选取 10–100 之间的 10 个数字，存入一个数组，并排序

```js
function getSortedRandomNumber() {
  var arr = [];
  for (var i = 0; i < 10; i++) {
    arr.push(Math.floor(Math.random() * 90) + 10);
  }
  return arr.sort(function(a, b) {
    return a - b;
  });
}
```

#### 有这样一个 URL：`http://item.taobao.com/item.htm?a=1&b=2&c=&d=xxx&e`，请写一段 JS 程序提取 URL 中的各个 GET 参数(参数名和参数个数不确定)，将其按 key-value 形式返回到一个 json 结构中，如{"a":"1","b":"2","c":"","d":"xxx","e":"undefined"}

```js
function getQueryJson(URLstr) {
  var queryIndex = URLstr.indexOf("?") + 1;
  var queryStr = URLstr.slice(queryIndex); //a=1&b=2&c=&d=xxx&e
  var arr = queryStr.split("&"); //[ a=1, b=2, c=, d=xxx, e ]
  var obj = {};
  var resultArr = [];
  for (var i = 0; i < arr.length; i++) {
    var tempArr = arr[i].split("=");
    obj[tempArr[0]] = tempArr[1];
  }
  for (var key in obj) {
    resultArr.push('"' + key + '"' + ":" + '"' + obj[key] + '"');
  }
  return "{" + resultArr.join(",") + "}"; //{"a":"1","b":"2","c":"","d":"xxx","e":"undefined"}
}
```

#### 写一个 function，清除字符串前后的空格。（兼容所有浏览器）

```js
if (typeof String.prototype.trim !== "function") {
  String.prototype.trim = function() {
    return this.replace(/^\s*|\s*$/g, "");
  };
}
```

#### 获取页面所有的 checkbox

```js
var resultArr = [];
var input = document.querySelectorAll("input");
for (var i = 0; i < input.length; i++) {
  if (input[i].type == "checkbox") {
    resultArr.push(input[i]);
  }
}
//resultArr即中获取到了页面中的所有checkbox
```

#### 已知有字符串 foo="get-element-by-id",写一个 function 将其转化成驼峰表示法"getElementById"

```js
function getCamelCase(str) {
  var arr = str.split("-");
  var resultArr = [];
  for (var i = 0; i < arr.length; i++) {
    if (i === 0) {
      resultArr.push(arr[i]);
    } else {
      resultArr.push(arr[i].charAt(0).toUpperCase() + arr[i].slice(1));
    }
  }
  return resultArr.join("");
}
```

#### 移动端边框 1 像素问题

新项目中 -> _viewport + rem 实现_

同时通过设置对应 viewport 的 rem 基准值，这种方式就可以像以前一样轻松愉快的写 1px 了。
在 devicePixelRatio = 2 时，输出 viewport：

```html
<meta
  name="viewport"
  content="initial-scale=0.5, maximum-scale=0.5, minimum-scale=0.5, user-scalable=no"
/>
```

在 devicePixelRatio = 3 时，输出 viewport：

```html
<meta
  name="viewport"
  content="initial-scale=0.333333333, maximum-scale=0.333333333, minimum-scale=0.333333333, user-scalable=no"
/>
```

在旧项目中 -> _伪类 + transform 实现_

原理是把原先元素的 border 去掉，然后利用 :before 或者 :after 重做 border ，并 transform 的 scale 缩小一半，原先的元素相对定位，新做的 border 绝对定位。
单条 border 样式设置：

```css
.scale-1px {
  position: relative;
  border: none;
}
.scale-1px:after {
  content: "";
  position: absolute;
  bottom: 0;
  background: #000;
  width: 100%;
  height: 1px;
  -webkit-transform: scaleY(0.5);
  transform: scaleY(0.5);
  -webkit-transform-origin: 0 0;
  transform-origin: 0 0;
}
```

四条 boder 样式设置:

```css
.scale-1px {
  position: relative;
  margin-bottom: 20px;
  border: none;
}
.scale-1px:after {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  border: 1px solid #000;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  width: 200%;
  height: 200%;
  -webkit-transform: scale(0.5);
  transform: scale(0.5);
  -webkit-transform-origin: left top;
  transform-origin: left top;
}
```

最好在使用前也判断一下，结合 JS 代码，判断是否 Retina 屏：

```js
if (window.devicePixelRatio && devicePixelRatio >= 2) {
  document.querySelector("ul").className = "scale-1px";
}
```
