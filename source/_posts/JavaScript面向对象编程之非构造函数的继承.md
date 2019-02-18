---
title: JavaScript面向对象编程之非构造函数的继承
date: 2017-06-01 16:46:36
categories: JavaScript
tags: 面向对象
---

## 什么是"非构造函数"的继承？

比如，现在有一个对象，叫做"中国人"。

```js
var Chinese = {
  nation: "中国"
};
```

还有一个对象，叫做"医生"。

```js
var Doctor = {
  career: "医生"
};
```

请问怎样才能让"医生"去继承"中国人"，也就是说，我怎样才能生成一个"中国医生"的对象？
这里要注意，这两个对象都是普通对象，不是构造函数，无法使用构造函数方法实现"继承"。

<!--more-->

## object()方法

json 格式的发明人 Douglas Crockford，提出了一个 object()函数，可以做到这一点。

```js
function object(o) {
  function F() {}
  F.prototype = o;
  return new F();
}
```

这个 object()函数，其实只做一件事，就是把子对象的 prototype 属性，指向父对象，从而使得子对象与父对象连在一起。
使用的时候，第一步先在父对象的基础上，生成子对象：

```js
var Doctor = object(Chinese);
```

然后，再加上子对象本身的属性：

```js
Doctor.career = "医生";
```

这时，子对象已经继承了父对象的属性了。

```js
alert(Doctor.nation); //中国
```

## 深拷贝

所谓"深拷贝"，就是能够实现真正意义上的数组和对象的拷贝。它的实现并不难，只要递归调用"浅拷贝"就行了。

```js
function deepCopy(p, c) {
  var c = c || {};
  for (var i in p) {
    if (typeof p[i] === "object") {
      c[i] = p[i].constructor === Array ? [] : {};
      deepCopy(p[i], c[i]);
    } else {
      c[i] = p[i];
    }
  }
  return c;
}
```

使用的时候这样写：

```js
var Doctor = deepCopy(Chinese);
```

现在，给父对象加一个属性，值为数组。然后，在子对象上修改这个属性：

```js
Chinese.birthPlaces = ["北京", "上海", "香港"];
Doctor.birthPlaces.push("厦门");
```

这时，父对象就不会受到影响了。

```js
alert(Doctor.birthPlaces); //北京, 上海, 香港, 厦门
alert(Chinese.birthPlaces); //北京, 上海, 香港
```

目前，jQuery 库使用的就是这种继承方法。
