---
title: JavaScript深入对象
date: 2018-03-07 23:03:40
categories: JavaScript
tags: 对象
---
### 创建对象
#### 简单做法
##### 使用Object构造函数创建
```js
// 对象实例的创建
var obj = new Object() 
obj.key = 'value'   //使用构造函数创建一个空对象，并赋值s
```
<!--more-->
##### 使用对象字面量表示法创建
```js
var obj = {
    key1: 'value1',
    key2: 'value2'
}
```
字面量表示法与 Object 构造函数创建法唯一的区别是，在字面量表示法里你可以给对象添加多个 键/值 对，但是在构造形式中你必须逐个添加属性。

现在ES6可以使用更简洁的方式创建对象：
```js
let age = 20;
let sex = 'sexy';

let obj = {
    name: 'jay',

    // 简洁表示法，等同于 age: age
    age, 

    // 简洁表示法，等同于 sayName： function() {}
    sayName(){},

    //属性名表达式，等同于 lover:'rose'
    ['lo'+'ver']: 'rose',

    // 属性名表达式，等同于 sexy: 'male'
    [sex]: 'male'

}
```
> 注意，属性名表达式如果是一个对象，默认情况下会自动将对象转为字符串 ‘[object Object]’，这一点要特别小心。

##### 工厂模式
即用函数来封装创建对象的细节。多次调用该函数来创建多个相似对象。
```js
function createPerson(name, age) {
    var o = {}
    o.name = name
    o.age = age
    o.sayName = function() {console.log(this)}
    return o
}
 
var a = createPerson('a', 20)
var b = createPerson('b', 22)
```
工厂模式虽然解决多创建多个相似对象的问题，但却没有解决对象识别的问题（即怎样知道一个对象的类型）。

#### 模仿‘类’的设计
##### 构造函数模式
构造函数包括像Array,Object这样的原生构造函数，他们在js运行时会自动出现在执行环境中。此外，我们可以创建自定义构造函数，从而定义自定义类型的属性和方法。现在构造函数重写上个例子：
```js
function Person(name,age) {
    this.name = name;
    this.age = age;
    this.sayName = function () {
        console.log(this);
    };
}
let a = new Person('a',20);

a instanceof Person // true
```
构造函数就是普通的函数，不存在特殊语法。构造函数与其他函数唯一的区别就在于调用他们的方式不同。任何函数只要通过`new`操作符来调用，那它就可以作为构造函数。
使用`new`操作符调用函数，或者说发生构造函数调用时，会自动执行下面操作：
1. 创建一个全新的对象。
2. 这个新对象会被执行[[Prototype]] 链接。
3. 这个新对象会绑定到函数调用时的this。
4. 如果函数没有返回其他对象，那么new表达式的函数调用会自动返回这个新对象。

由于构造函数调用时会自动执行 [[Prototype]] 链接，也就是把新对象的原型链指向构造函数的 prototype。所以使用`instanceof`或`isPrototypeOf`方法可以判断他们的类型。

上面这种构造函数解决了对象类型识别的问题，但是每个方法都要在每个实例上重新创建一遍，在上面的例子中，a 和 b 都有个名为`sayName()`的方法，这两个方法虽然名字、内容、功能相同，但却分别在 a 和 b 中都重新创建了一次，这是没有必要的。

更好的方法应该是将公用的方法放到他们的原型上，也就是接下来要说的原型模式。
##### 原型模式
所有函数都有一个不可枚举的prototype(原型)属性，这个属性时一个指针，指向一个对象。
```js
function Foo () {}

Foo.prototype // {}
```
上一节【构造函数模式】里面有说，`new`操作符会新建一个对象，并把该对象的原型链指向构造函数的 `prototype` 所指向的对象。

这里出现了一个重点词**原型链**，我们先解释下什么叫做原型链。
>原型链也被称为 [[Prototype]]链，是对象的内置属性。原型链是 ECMAScript 中实现继承的主要办法，其基本思想就是让一个引用类型继承另一个引用类型的属性和和方法。

例如我们新建个对象 a，然后给它指定它的原型链的指向：
```js
var a = {}
var b = {x: 2}
 
Object.setPrototypeOf(a, b)
 
a.x // 2
```
这个例子中我们通过`Object.setPrototypeOf()`方法把 a 的原型链指向 b，然后 a 就继承了 b 的属性，当查询 a 中没有的 x 属性时，会指向到 b.x 的值。

这里原理上说是委托比说是继承更符合真实情况。因为真实情况如上段所说是 a 把自身没有的属性查询委托给 b，如果 b 中也没 x 属性的话，系统会继续循着原型链往上查。所有普通的 [[Prototype]] 链最终都会指向内置的 `Object.prototype`。如果 `Object.prototype` 也没有的话就会提示 `undefined`。

这里提示一下，由于所有普通的对象都”源于“（或者说把 [[Prototype]] 链的顶端设置为）这个 `Object.prototype` 对象，所以它包含 Javascript 中许多通用的功能。

上面说过，new操作符会新建一个对象，并把该对象的原型链指向构造函数的 prototype 属性所指向的对象（即原型对象）。使用原型对象的好处是可以让所有对象实例共享它所包含的属性和方法。换句话说，不必在构造函数中定义对象实例的信息，而是可以将这些信息直接添加到原型对象中。例如：
```js
function Person() {}

Person.prototype = {
    name: 'h',
    sayName () {
        alert(this.name);
    }
}
var a = new Person()
 
a.sayName() // h
```
这种方法因为重写了Person.prototype,所以默认的Person.prototype.constructor也会丢失，如果要修复constructor,可以使用`Object.defineProperty()`.
```js
Object.defineProperty(Person.prototype,'constructor',{
    enumerable: false,
    value: Person
})
```
不过实例一般都是要有属于自己的全部属性的。所以日常开发中设计对象更常见的做法是构造函数模式跟原型模式组合使用：
```js
function Person (name) {
    this.name = name;
}

Person.prototype.sayName = function () {
    alert(this.name);
}

let a = new Person('Jack');
a.sayName(); // Jack
```
这里很像一些面向“类”的语言的行为，a 和 b 是 Person 类的实例，a 和 b 继承了 Person 类的特性。但实际上 JavaScript 和面向类的语言不同，它并没有类作为对象的抽象模式或者说蓝图。JavaScript 中只有对象。
##### 使用ES6 Class
上面说的原型是通过构造函数，定义并生成新对象。但这种写法跟传统的面向对象语言（比如C++和Java）差异很大，很容易让新学习这门语言的程序员感到困惑。ES6 提供了更接近传统语言的写法，引入了Class（类）这个概念，作为对象的模板。

不过需要注意的是， ES6 的 class 语法是并不是向 JavaScript 中引入了一种新的“ 类” 机制。 **class 基本上只是现有 [[Prototype]] 机制的一种语法糖。**

也就是说， class 并不会像传统面向类的语言一样在声明时静态复制所有行为。 如果你（ 有意或无意） 修改或者替换了父“ 类” 中的一个方法， 那子“ 类” 和所有实例都会受到影响， 因为它们在定义时并没有进行复制， 只是使用基于 [[Prototype]] 的实时委托。

ES6 的类，完全可以看作构造函数的另一种写法。
```js
class Foo {

}
typeof Foo // 'function'
Foo === Foo.prototype.constructor // true
```
面代码表明，类的数据类型就是函数，类本身就指向构造函数。

使用的时候，也是直接对类使用new命令，跟构造函数的用法完全一致。
```js
class Foo {
    constructor () {
        this.name = name;
    }
    sayName () {
        console.log(this.name);
    }
}

let a = new Foo('a');

a.sayName(); // a
```
构造函数的prototype 属性，在ES6的‘类’上继续存在，事实上，类的所有方法都定义上类的prototype 属性上面。
```js
class Foo {
    constructor () {}
    toString () {}
    toValue () {}
}

// 等同于

Foo.prototype = {
    toString(){},
    toValue () {}
}
```
由于类的方法都定义在 prototype 对象上面，所以类的新方法可以添加在 prototype 对象上面。Object.assign方法可以很方便地一次向类添加多个方法。
```js
class Foo {
  constructor(){}
}
 
Object.assign(Foo.prototype, {
  toString(){},
  toValue(){}
});
```
ES6 Class 与 普通构造函数的不同点主要有 4 个：

(1)constructor 方法

`constructor`方法是类的默认方法，通过 new 命令生成对象实例时，自动调用该方法。一个类必须有`constructor`方法，如果没有显式定义，一个空的`constructor`方法会被默认添加。

`constructor`方法默认返回实例对象（即this），完全可以指定返回另外一个对象。
```js
class Foo {
  constructor() {
    return Object.create(null);
  }
}
 
new Foo() instanceof Foo    // false
```
上面代码中，constructor函数返回一个全新的对象，结果导致实例对象不是 Foo 类的实例。

类的构造函数，不使用new是没法调用的，会报错。这是它跟普通构造函数的一个主要区别，后者不用new也可以执行。
```js
class Foo {
constructor() {
return Object.create(null);
}
}
Foo()
// TypeError: Class constructor Foo cannot be invoked without 'new'
```
(2)类的内部所有定义的方法，都是不可枚举的（non-enumerable）。这一点与普通构造函数的行为不一致。
```js
/ class
class Foo {
    constructor(name) {
        this.name = name
    }
    sayName(){
        console.log(this.name)
    }
}
 
Object.keys(Foo.prototype)  // []
Object.getOwnPropertyNames(Foo.prototype)   // ["constructor","sayName"]
```
```js
//构造函数
function Foo(name) {
    this.name = name
}
 
Foo.prototype.sayName = function() {
    alert(this.name) 
}
 
Object.keys(Foo.prototype)  // ["sayName"]
Object.getOwnPropertyNames(Foo.prototype)   // ["constructor", "sayName"]
```
上面代码中，sayName方法是 Foo 类内部定义的方法，它是不可枚举的。而定义在 Foo.prototype 中的话则是可枚举的。

(3) Class不存在变量提升（hoist），这点与ES5完全不同。
```js 
new Foo(); // ReferenceError
class Foo {}
```
上面代码中，Foo 类使用在前，定义在后，这样会报错，因为 ES6 不会把类的声明提升到代码头部。这种规定的原因与下文要提到的继承有关，必须保证子类在父类之后定义。

(4)子类的继承

Class 之间可以通过 `extends` 关键字实现继承，这比普通构造函数通过修改原型链实现继承，要清晰和方便很多。
```js
class Foo {}
class Bar extends Foo {}
```
上面代码定义了一个 Bar 类，该类通过extends关键字，继承了 Foo 类的所有属性和方法。但是由于没有部署任何代码，所以这两个类完全一样，等于复制了一个 Foo 类。下面，我们在 Bar 内部加上代码。
```js
class Foo {
    constructor (name,age) {
        this.name = name;
        this.age = age;
    }

    sayProfile () {
        return `hello, my name is ${this.name}, ${this.age} years old`
    }
}

class Bar extends Foo {
    constructor (name ,age, lover) {
        super(name,age);// 调用父类的 constructor(name,age), 约等于普通构造函数中的 Foo.call(this, name, age)
        this.age = Number(this.age) + 5;
        this.lover = lover;
    }

    sayIntro() {
        alert(super.sayProfile() + `, my lover is ${this.lover}, again, I am ${this.name}`)
    }
}

var a = new Bar('Jack', '20', 'Rose')
 
a.sayIntro()    // "hello, my name is Jack, 25 years old, my lover is Rose, again, I am 
```
上面代码中，子类 Bar 的`constructor`方法和`sayIntro`方法之中，都出现了`super`关键字，`super`这个关键字，既可以当作函数使用，也可以当作对象使用。在这两种情况下，它的用法完全不同。

第一种情况，`super`作为函数调用时，代表父类的构造函数。ES6 要求，子类的构造函数必须执行一次`super`函数。在上面的代码中 子类 Bar 的构造函数中`super(name, age)`相当于`Foo.prototype.constructor.call(this, name, age)`。作为函数时，`super()`只能用在子类的构造函数之中，用在其他地方就会报错。

**要注意的是，子类必须在`constructor`方法中调用`super`方法，否则新建实例时会报错**。这是因为子类没有自己的 this 对象，而是继承父类的 this 对象，然后对其进行加工。如果不调用`super`方法，子类就得不到 this 对象。在子类的构造函数中，只有调用`super`之后，才可以使用 this 关键字，否则会报错。这是因为子类实例的构建，是基于对父类实例加工，只有`super`方法才能返回父类实例。

普通构造函数的继承，实质是先创造子类的实例对象 this，然后再将父类的方法添加到 this 上面`（Foo.call(this)）`。**ES6 Class 的继承机制完全不同，实质是先创造父类的实例对象 this（所以必须先调用`super`方法），然后再用子类的构造函数修改 this。**

如果子类没有定义`constructor`方法，这个方法会被默认添加，代码如下。也就是说，不管有没有显式定义，任何一个子类都有`constructor`方法：
```js
constructor (...args) {
    super(...args);
}
```
第二种情况，**`super`作为对象时，在普通方法中，指向父类的原型对象；在静态方法中，指向父类。**上面代码代码中的 `super.sayProfile()`就是将`super`当作一个对象使用。这时，`super`在普通方法之中，指向 `Foo.prototype`，所以`super.sayProfile()`就相当于`Foo.prototype.sayProfile()`。

ES6 规定，通过`super`调用父类的方法时，`super`会绑定子类的 this。所以`sayProfile`输出的`this.age`是子类 Bar 的 age 25岁。

刚刚说到在静态方法中`super`指向父类。
> 静态方法
>
> 类相当于实例的原型，所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上 static 关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。

```js
class Foo {
    static sayHi () {
        return 'hello';
    }
}

Foo.sayHi(); // hello
 
var a = new Foo();
a.sayHi(); // TypeError: a.sayHi is not a function
```
上面代码中`ayHi()`就是 Foo 的静态方法，它只能通过 Foo 调用，不能通过 Foo 的实例调用。

如果`super`作为对象用在静态方法中，这时`super`将指向父类，而不是父类的原型对象。
```js
class Foo {
    static sayHi() {  // 父类
        return 'hello';
    }
    sayHi () {  // 原型
        return 'oops';
    }
}

class Bar extends Foo {
    static sayBob () {
        return super.sayHi() + 'Bob';
    }
}

Bar.sayBob (); // 'hello Bob'
```
#### 面向委托的设计（对象关联风格）
有些开发者比较诟病这种模仿“类”的设计模式，因为继承意味着复制，JavaScript（默认）并不会复制对象属性。相反，JavaScript 会在两个对象之间创建一个关联（我们把这种关联成为原型链），这样对象就可以通过委托访问另一个对象的属性和函数。委托这个术语可以更加准确地描述 JavaScript 中的对象关联机制。

即便通过 ES6 class 让 js 看起来更像“类”，也只是一种模仿的假象。在传统的面向类的语言中，类定义之后就不会进行修改，所以类的设计模式就不支持修改，但是 Javascript 最强大的特性就是它的动态性，任何对象的定义都可以修改（除非你把它定义为不可变）。

一些开发者认为 ES6 的 class 想伪装成一种很好的语法问题的解决方案，但实际上却让问题更难解决且让 JavaScript 更难理解。因为它隐藏了 JavaScript 对象最重要的机制——对象之间的实时委托机制。让本来简洁优雅的 [[Prototype]] 机制变的非常别扭。

我们比较下继承与委托设计的区别。

我们先想想类（继承）的设计方法：先定义一个通用的父类 F，在父类 F 中定义所有任务都有的行为，接着定义子类 a 和 b，他们都继承自父类并且会添加一些特殊行为来处理对应的任务。

再用委托的思想来考虑同样的问题：首先先定义一个对象 F，它包含所有任务都可以使用（委托）的具体行为。接着，对于每个任务你都会定义一个对象来储存对应的数据和行为，你会把特定的任务对象都关联到 F 功能对象上面，让他们在需要的时候可以进行委托。基本你可以想象成 a 和 F 之间是兄弟关系，a 完成不了的任务东西都委托给 F完成。

> JavaScript 中原型链实际上是委托的关系而不是继承。委托行为意味着某些对象在找不到属性或方法引用时会把这个请求委托给另一个对象。

我们先看一个使用类设计风格的例子：
```js
function  Foo (name) {
    this.name = name;
}
Foo.prototype.intro = function() {
    return 'my name is ' + this.name;
}

function Bar (name) {
    Foo.call(this,name);
}
Bar.prototype = Object.create(Foo.prototype);
Bar.prototype.speak = function(){alert(this.intro())};

var a = new Bar('a');
a.speak(); // my name is a
```
这个例子里子类 Bar 继承了父类 Foo，然后生成了 a 这个实例，a 通过 new 构造函数委托了 `Bar.prototype`，`Bar.prototype` 通过`Object.create()`方法委托了 `Foo.prototype()`。这种风格很常见，你应该已经熟悉了。

当然我们可以用 class 让这段代码看起来更简洁明了：
```js
class Foo {
    constructor(name) {
        this.name = name
    }
    intro() { return 'my name is ' + this.name }
}
 
class Bar extends Foo {
    constructor(name) {
        super(name)
    }
    speak() { alert(super.intro()) }
}
 
var a = new Bar('a')
 
a.speak()
```
现在再来看看同例的对象关联风格的设计：
```js
Foo = {
    init: function(name) {
        this.name = name
    },
    intro: function() {
        return 'my name is ' + this.name
    }
}
 
Bar = Object.create(Foo)
Bar.speak = function() {
    alert(this.intro())
}
 
var a = Object.create(Bar)
a.init('a')
 
a.speak()
```
这段代码中我们同样利用 [[Prototype]] 把 a 委托给 Bar 并把 Bar 委托给 Foo，和上段代码一样，我们仍然实现了三个对象的关联。

但非常重要的一点是，这段代码简洁了许多，我们只是把对象关联起来，并不需要那些既复杂又令人困惑的模仿类的行为（构造函数、原型以及 new）。
### 对象的内容
对象的内容是由一些储存在特定命名位置的（任意类型的）值组成的，我们称之为属性。
#### getter与setter
##### getter
get 语法将一个对象属性绑定到查询该属性时将被调用的一个函数上。该方法会覆盖单个属性默认的 [[Get]] 操作（获得属性值的操作）。

用法：
> {get prop() { … } } // prop 为要绑定到给定函数的属性名
>
>{get [expression]() { … } } //从ECMAScript 2015 (ES6)开始，还可以使用一个计算的属性名的表达式绑定到给定的函数。

例如：
```js
var myObj = {
    get a () {
        return 2;
    }
};

// 也可以通过 defineProperty 创建
Object.defineProperty(myObj, 'b', {
    get: function () {return this.a*2},
    enumable: true
})

myObj.a // 2
myObj.b // 4

myObj.a = 3 
myObj.a  // 2
```
如上面例子，不管是对象文字语法中的`get a() { .. }`， 还是`defineProperty(...)`中的显式定义， 二者都会在对象中创建一个不包含值的属性，对于这个属性的访问会自动调用一个隐藏函数，它的返回值会被当作属性访问的返回值。

同时由于我们只定义了 a 的 getter， 所以对 a 的值进行设置时 set 操作会忽略赋值操作，且不会抛出错误。 为了让属性更合理， 我们还应当定义 setter。
##### setter
set 语法会覆盖单个属性默认的 [[Put]] 操作（赋值操作）。

通常来说 getter 和 setter 是成对出现的（ 只定义一个的话通常会产生意料之外的行为）。

例如：
```js
var myObj = {
    get a () {
        return this._a_;
    },
    set a(val) {
        this._a_ = val * 2;
    }
}

myObj.a = 2;
myObj.a // 4
```
注意，在本例中， 实际上我们把赋值（ [[Put]]） 操作中的值 2 存储到了另一个变量\_a\_中。 名称\_a\_只是一种惯例， 没有任何特殊的行为，和其他普通属性一样。
#### 遍历对象属性
##### for...in
`for keys in object` 循环可以遍历对象的可枚举属性列表（包括 [[Prototype]] 原型链）。

`for..in`循环是无法直接获取属性值的，你需要手动获取属性值。

需要注意的是遍历对象属性时的顺序是不确定的，在不同的 JavaScript 引擎中可能不一样。
##### for...of
`for..of`循环首先会向被访问的对象请求一个迭代器对象，然后通过调用迭代器对象的 `next()` 方法来遍历所有返回值。

我们先看一下迭代器的定义：
> **可迭代协议**
>
>可迭代协议允许 JavaScript 对象去定义或定制它们的迭代行为, 例如（定义）在一个 for..of 结构中什么值可以被循环（得到）。一些内置类型都是内置的可遍历对象并且有默认的迭代行为, 比如 Array、Map, 另一些类型则不是 (比如 Object) 。
>
>为了变成可遍历对象， 一个对象必须实现 @@iterator 方法, 意思是这个对象（或者它原型链上的某个对象）必须有一个名字是 Symbol.iterator 的属性:

数组有内置的 `@@iterator`， 因此`for..of`可以直接应用在数组上:
```js
var myArr = [1,2,3];
for(var v of myArr){
    console.log(v);
}
// 1
// 2
// 3
```
我们再通过数组的`Symbol.iterator`属性看看`@@iterator`是怎么工作的：
```js
var myArr = [1,2,3];
it = myArr[Symbol.iterator]()
it.next()   // {value: 1, done: false}
it.next()   // {value: 2, done: false}
it.next()   // {value: 3, done: false}
it.next()   // {value: undefined, done: true}
```
如你所见，调用迭代器的`next()`方法会返回形式为`{value: .., done: ..}`的值，`value`是当前的遍历值，`done`是一个布尔值，表示是否还有可以遍历的值。

上面示例中需要注意的一点，我们使用符号`Symbol.iterator`来获取对象的`@@iterator`内部属性。引用类似 `iterator` 的特殊属性时要使用符号名，而不是符号包含的值。此外，虽然看起来很像一个对象，但`@@iterator`本身并不是迭代器对象，而是返回迭代器对象的函数——这点非常精妙并且重要。

可是普通对象没有内置的`@@iterator`，所以无法完成`for..of`遍历。制定者之所以这样做，有许多复杂的原因，不过简单来说，这样做是为了避免影响未来的对象类型。

但我们可以给任何想遍历的对象自定义`@@iterator`，例如：
```js
var myObj = {a:1, b:2}
Object.defineProperty(myObj, Symbol.iterator, {
    enumerable: false,
    writable: false,
    configurable: true,
    value: function() {
        var o = this;
        var idx = 0;
        var ks = Object.keys(o);
        return {
            next: function() {
                return {
                    value: o[ks[idx++]],
                    done: (idx > ks.length)
                }
            }
        }
    }
})
// 手动遍历 myObj
var it = myObj[Symbol.iterator]()

it.next()   // {value: 1, done: false}
it.next()   //{value: 2, done: false}
it.next()   //{value: undefined, done: true}

// 用 for..of 遍历 myObj
for(var v of myObj) {
    console.log(v)
}
// 1
// 2
```
#### 对象的代理Proxy
ES6 中引入的 Proxy 是一个元编程的特性。元编程是指的是开发人员对 “语言本身进行编程”。一般是编程语言暴露了一些 API，供开发人员来操作语言本身的某些特性。

Proxy 对象用于定义基本操作的自定义行为 (例如属性查找，赋值，枚举，函数调用等)。Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。也就说它可以 “代理” 对象的原生行为，替换为执行自定义行为。

Proxy语法如下：
```js
let p = new Proxy(target,handler);
```
其中，`new Proxy()`表示生成一个Proxy实例，`target`参数表示所要拦截的目标对象，`handler`参数也是一个对象，用来定制拦截行为。

举个栗子，我们知道当我们要获取对象的属性值的时候，对象内部会调用 get 方法去获取这个属性，如果该属性不存在在该对象中，则会继续向 [[Prototype]] 原型链向上寻找，一直找到 Object.prototype。

如果我们按上一节的方法给该属性设置了`get()`方法，则查询该属性值时会返回`get()` 方法的返回值。Proxy 可以给所有查询不到的属性设置统一的`get()`方法。

例如：
```js
var a = {x: 2}
var obj = new Proxy(a, {
    get: function(target, name){
        return name in target ? target[name] : 3
    }
})
obj.x // 2
obj.y // 3
obj.z // 3
a.y // undefined
```
需要注意的是，要使得 Proxy 起作用，必须针对 Proxy 实例（上例是proxy对象）进行操作，而不是针对目标对象（上例是空对象）进行操作。

如果`handler`没有设置任何拦截，那就等同于直接通向原对象。
```js
var a = {x:2}
var obj = new Proxy(a,{})

obj.y = 3
a.y = 3;
```
有一个技巧是把 Proxy 实例作为其他对象的原型对象，这样就可以让其他对象继承（或者说委托？）Proxy 实例的拦截方法。
```js
var obj = new Proxy ({},{
    get:function (){
        return 233;
    }
})

var a = Object.create(obj);

a.someprop // 233
```
此外，Proxy 对象还提供了一个revoke方法，可以随时注销所有的代理操作。
```js
var p = Proxy.revocable({}, {
    get() {
        return 1
    }
});

var a = p.proxy
a.x // 1
p.revoke()
a.x // Uncaught TypeError: Cannot perform 'get' on a proxy that has been revoked
```
`Proxy.revocable`方法返回一个对象，该对象的`proxy`属性是 Proxy 实例，`revoke`属性是一个函数，可以取消 Proxy 实例。上面代码中，当执行`revoke`函数之后，再访问 Proxy 实例，就会抛出一个错误。

`Proxy.revocable`的一个使用场景是，目标对象不允许直接访问，必须通过代理访问，一旦访问结束，就收回代理权，不允许再次访问。

包括 `get()`、`set()` 在内，Proxy 支持的拦截操作有十多种，包含了大部分对象的方法操作的拦截。具体请查阅 ECMAScript 6 入门 及 MDN 文档

### 对象方法
#### 原型相关 create,setPrototypeOf,getPrototypeOf
##### Object.create(proto, [ propertiesObject ])
该方法使用指定的原型对象和其属性创建了一个新的对象。可理解为该方法会创建一个对象并把这个对象的 [[Prototype]]关联到指定对象。

需要注意的是，`Object.create(null)`会创建一个拥有空（或者 null）[[Prototype]] 链的对象，这个对象无法进行委托。由于这个对象没有原型链，所以`instanceof`操作符无法进行判断，因此总是返回 false。这些特殊的空 [[Prototype]] 对象通常被称作“字典”，他们完全不会受到原型链的干扰，因此非常适合用来储存数据。
##### Object.setPrototypeOf(obj, prototype)
该方法是ES6的新方法，用于设置一个指定的对象的原型 ( 例如,内置的 [[Prototype]] 属性）到另一个对象或 null。可以代替 ES5 的`Object.create`。

例如有两个对象 a 和 b，我们需要把 b 设为 a 的原型，以便 a 可以继承 b 的属性：
```js
var a = {x: 1};
var b = {y: 2};
Object.setPrototypeOf(a,b);

a.x //1
a.y // 2

Object.getOwnPropertyNames(a) // ["x"]
```
上例中把 b 设为 a 的原型，因此当向 a 查询 y 时，由于 a 本身没有 y 属性，所以会循着原型链查询到 b 的 y。

再例如有两个函数（函数也是对象哦！） Bar 和 Foo，我们需要把 Bar.prototype 关联到 Foo.prototype:
```js
// 创建 Foo
function Foo(name) {this.name = name}
Foo.prototype.sayName= function() {return this.name}

// 创建 Bar
function Bar(name){Foo.call(this, name)}

//关联 prototype
// ES6 之前需要抛弃默认的 Bar.prototype
Bar.prototype = Object.create(Foo.prototype)

// ES6 开始可以直接修改现有的 Bar.prototype
Object.setPrototypeOf(Bar.prototype, Foo.prototype)

var a = new Bar('a')
a.sayName()
```
如果不考虑`Object.create(..)`方法带来的轻微性能损失（抛弃的对象需要进行垃圾回收），它其实比 ES6 及其之后的方法更短切可读性更高。
##### Object.getPrototypeOf(obj)
该方法返回指定对象的原型（即内部 [[Prototype]] 属性的值）。如果没有继承属性，则返回 null 。
```js
var a = {}
var b = Object.create(a)
 
Object.getPrototypeOf(b) === a // true
```