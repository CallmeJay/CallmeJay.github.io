---
title: JavaScript常用几种继承方案
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

```js
var person = {
  name: "Nicholas",
  friends: ["Shelby", "Court", "Van"]
};

var anotherPerson = create(person);
anotherPerson.name = "Greg";
anotherPerson.friends.push("Rob");

var yetAnotherPerson = create(person);
yetAnotherPerson.name = "Linda";
yetAnotherPerson.friends.push("Barbie");

alert(person.friends);   //"Shelby,Court,Van,Rob,Barbie"
```

缺点：

* 原型链继承多个实例的引用类型属性指向相同，存在篡改的可能。
* 无法传递参数

另外，ES5中存在*Object.create()*的方法，能够代替上面的object方法。

### 寄生式继承

核心：在原型式继承的基础上，增强对象，返回构造函数

```js
function createAnother(original){
  var clone = create(original); // 通过调用 create() 函数创建一个新对象
  clone.sayHi = function(){  // 以某种方式来增强对象
    alert("hi");
  };
  return clone; // 返回这个对象
}
```

函数的主要作用是为构造函数新增属性和方法，以**增强函数**

```js
var person = {
  name: "Nicholas",
  friends: ["Shelby", "Court", "Van"]
};
var anotherPerson = createAnother(person);
anotherPerson.sayHi(); //"hi"
```

缺点（同原型式继承）;

### 寄生组合式继承

结合借用构造函数传递参数和寄生模式实现继承

```js
function inhertPrototype(subType,superType){
  var prototype = Object.create(superType.prototype); // 创建对象，创建父类原型的一个副本
  prototype.constructor = subType;                    // 增强对象，弥补因重写原型而失去的默认的constructor 属性
  subType.prototype = prototype;                      // 指定对象，将新创建的对象赋值给子类的原型
}

// 父类初始化实例属性和原型属性
function SuperType(name){
  this.name = name;
  this.color = ['red','yellow'];
}

SuperType.prototype.sayName = function(){
  alert(this.name);
};

// 借用构造函数传递增强子类实例属性（支持传参和避免篡改）
function SubType(name,age){
  SuperType.call(this,name);
  this.age = age;
}

// 将父类原型指向子类
inheritPrototype(SubType, SuperType);

// 新增子类原型属性
SubType.prototype.sayAge = function(){
  alert(this.age);
}

var instance1 = new SubType("xyc", 23);
var instance2 = new SubType("lxy", 23);

instance1.colors.push("2"); // ["red", "blue", "green", "2"]
instance1.colors.push("3"); // ["red", "blue", "green", "3"]

```
![寄生式组合继承](https://user-gold-cdn.xitu.io/2018/10/30/166c2c0109df5438?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

这个例子的高效率体现在它只调用了一次*SuperType*构造函数，并且因此避免了在*SubType.prototype* 上创建不必要的、多余的属性。于此同时，原型链还能保持不变；因此，还能够正常使用*instanceof*和*isPrototypeOf()*

**这是最成熟的方法，也是现在库实现的方法**

### ES6类继承extends

*extends*关键字主要用于类声明或者类表达式中，以创建一个类，该类是另一个类的子类。其中*constructor*表示构造函数，一个类中只能有一个构造函数，有多个会报出*SyntaxError*错误,如果没有显式指定构造方法，则会添加默认的*constructor*方法，使用例子如下。

```js
class Rectangle {
    // constructor
    constructor(height, width) {
        this.height = height;
        this.width = width;
    }

    // Getter
    get area() {
        return this.calcArea()
    }

    // Method
    calcArea() {
        return this.height * this.width;
    }
}

const rectangle = new Rectangle(10, 20);
console.log(rectangle.area);
// 输出 200

-----------------------------------------------------------------
// 继承
class Square extends Rectangle {

  constructor(length) {
    super(length, length);

    // 如果子类中存在构造函数，则需要在使用“this”之前首先调用 super()。
    this.name = 'Square';
  }

  get area() {
    return this.height * this.width;
  }
}

const square = new Square(10);
console.log(square.area);
// 输出 100
```

*extends*继承的核心代码如下，其实现和上述的寄生组合式继承方式一样

```js
function _inherits(subType, superType) {
  
    // 创建对象，创建父类原型的一个副本
    // 增强对象，弥补因重写原型而失去的默认的constructor 属性
    // 指定对象，将新创建的对象赋值给子类的原型
    subType.prototype = Object.create(superType && superType.prototype, {
        constructor: {
            value: subType,
            enumerable: false,
            writable: true,
            configurable: true
        }
    });

    if (superType) {
        Object.setPrototypeOf
            ? Object.setPrototypeOf(subType, superType) 
            : subType.__proto__ = superType;
    }
}
```

### 总结

1、函数声明和类声明的区别
函数声明会提升，类声明不会。首先需要声明你的类，然后访问它，否则像下面的代码会抛出一个ReferenceError。

```js
let p = new Rectangle();
// ReferenceError

class Rectangle {}
```

2、ES5继承和ES6继承的区别

* ES5的继承实质上是先创建子类的实例对象，然后再将父类的方法添加到this上（Parent.call(this)）.
* ES6的继承有所不同，实质上是先创建父类的实例对象this，然后再用子类的构造函数修改this。因为子类没有自己的this对象，所以必须先调用父类的super()方法，否则新建实例报错。