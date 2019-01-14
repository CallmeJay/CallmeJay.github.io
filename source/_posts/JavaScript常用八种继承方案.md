---
title: JavaScript常用八种继承方案
date: 2019-01-14 09:59:22
categories: JavaScript
tags: 继承
---

### 原型链继承

> 构造函数，原型和实例之间的关系： 每个构造函数都有一个原型对象，原型对象都包含一个指向构造函数的指针，实例都包含一个原型对象的指针。

继承的本质是复制，即重写原型对象，代之以新类型的实例。

<!--more-->

```js
function Animals(){
  this.type = 'animals';
}

Animals.prototype.getType = function(){
  return this.type;
}

function Cat(){
  this.name = 'cat';
}
// 这里是关键，创建 Animals 的实例，并将该实例赋值给 Cat.prototype
Cat.prototype = new Animals();

var cat = new Cat();

console.log(cat.getType());
```

![原型链继承](https://user-gold-cdn.xitu.io/2018/10/30/166c2c0107fd80c7?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

原型链方案存在的缺点：多个实例对*引用类型*的操作会被篡改。

```js
function SuperType(){
  this.colors = ["red", "blue", "green"];
}
function SubType(){}

SubType.prototype = new SuperType();

var instance1 = new SubType();
instance1.colors.push("black");
alert(instance1.colors); //"red,blue,green,black"

var instance2 = new SubType(); 
alert(instance2.colors); //"red,blue,green,black"
```

### 借用构造函数继承

使用父类的构造函数来增强子类实例，等同于复制父类的实例给子类（不使用原型）

```js
function Animals(){
  this.colors = ["red", "blue", "green"];
}

function Cat(){
  //继承自Animals
  Animals.call(this);
}

var cat1 = new Cat();
alert(cat1.colors);//"red,green,blue,black"

var cat2 = new Cat();
alert(cat2.colors);//"red,green,blue"
```

核心代码是*Animals.call(this)*，创建子类实例时调用*Animals*构造函数，于是*Cat*的每个实例都会将*Animals*中的属性复制一份。

缺点：

* 只能继承父类的实例属性和方法，不能继承原型属性/方法
* 无法实现复用，每个子类都有父类实例函数的副本，影响性能

### 组合继承

组合上述两种方法就是组合继承。用原型链实现对原型属性和方法的继承，用借用构造函数技术来实现实例属性的继承。

```js
function SuperType(name){
  this.name = name;
  this.colors = ["red", "blue", "green"];
}
SuperType.prototype.sayName = function(){
  alert(this.name);
};

function SubType(name, age){
  // 继承属性
  // 第二次调用SuperType()
  SuperType.call(this, name);
  this.age = age;
}

// 继承方法
// 构建原型链
// 第一次调用SuperType()
SubType.prototype = new SuperType(); 
// 重写SubType.prototype的constructor属性，指向自己的构造函数SubType
SubType.prototype.constructor = SubType; 
SubType.prototype.sayAge = function(){
    alert(this.age);
};

var instance1 = new SubType("Nicholas", 29);
instance1.colors.push("black");
alert(instance1.colors); //"red,blue,green,black"
instance1.sayName(); //"Nicholas";
instance1.sayAge(); //29

var instance2 = new SubType("Greg", 27);
alert(instance2.colors); //"red,blue,green"
instance2.sayName(); //"Greg";
instance2.sayAge(); //27
```

![组合继承](https://user-gold-cdn.xitu.io/2018/10/30/166c2c010c537ff8imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

缺点：

* 第一次调用*SuperType()*：给*SubType.prototype*写入两个属性name，color。
* 第二次调用*SuperType()*：给*instance1*写入两个属性name，color。

实例对象*instance1*上的两个属性就屏蔽了其原型对象*SubType.prototype*的两个同名属性。所以，组合模式的缺点就是在使用子类创建实例对象时，其原型中会存在两份相同的属性/方法。

### 原生式继承

利用一个空对象作为中介，将某个对象直接赋值给空对象构造函数的原型。

```js
function create(obj){
  fuction F(){};
  fn.prototype = obj;
  return new F();
}
```

create()对传入其中的对象执行了一次*浅复制*，将构造函数F的原型直接指向传入的对象。

