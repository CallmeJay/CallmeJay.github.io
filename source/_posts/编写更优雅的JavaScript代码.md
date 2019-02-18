---
title: 编写更优雅的JavaScript代码
date: 2018-09-10 13:46:33
categories: JavaScript
tags: ES6
---

### 代码技巧

#### 优先 ES6 新特性写法

```js
// 箭头函数
function foo(){
  console.log('hello world')
}

const foo = () => console.log('hello world')

// 数组去重
const formatArray = arr => [...new Set(arr)]

// 数组合并
const newArr = [...arr1, ...arr2, 'value']

// 对象浅拷贝
const newObj = {...obj}

// 解构赋值
const person = {name: 'bao', age: 18}
const { name, age } = person

// 常见对象属性取值前判断对象是否存在
// 如下是 react + antd 渲染 table 的简单代码，对象存在性判断 + 默认值设置

render(){
  const { downloadList } = this.props.store.downloadList
  let items = downloadList && downloadList.items || []
  let itemCount = downloadList && downloadList.itemCount || 10

  return <Table dataSource={items} pagination={{total: itemCount}} />
}
// 优化后
render(){
  const { items, itemCount } = this.props.manageStore.downloadList || {}
  return <Table dataSource={items || []} pagination={{total: itemCount || 10}}/>
}
```

<!--more-->

#### 优化逻辑判断语句

大量的 if else 逻辑判断难以维护，且性能较差，可用多种方式代替

```js
// 对象配置法
// 函数内部有条件判断，且 return 值时，满足条件立即return，而不要在结尾return
const foo = v => {
  if (v === "name") {
    return "bao";
  } else if (v === "age") {
    return "18";
  } else if (v === "height") {
    return "180";
  }
};
const cfg = {
  name: "bao",
  age: "18",
  height: "180"
};
const foo = v => cfg[v];

// 数组配置法
if (value === "hello" || value === "world" || value === "blabla") {
  // ...
}
// 配置数组形式
const rightValue = ["hello", "world", "blabla"];
if (rightValue.includes[value]) {
  // ...
}
```

#### 善用 && 、 || 和 三元运算

```js
if (name === "bao") {
  someFunc();
}

name === "bao" && someFunc();

if (name === "bao") {
  someFunc();
} else {
  elseFunc();
}

name === "bao" ? someFunc() : elseFunc();
```

#### 对象属性变量应用

如在 react 中，调用 action 方法来获取数据，不同条件执行不同方法

```js
if (isMember) {
  let res = await actions.getMemberInfo(params);
} else {
  let res = await actions.getCommonUserInfo(params);
}

const actionName = isMember ? "getMemberInfo" : "getCommonUserInfo";
let res = await actions[actionName](params);
```

#### 用 Array.map(), Array.filter() 代替数组 for 循环实现简易写法

```js
let arr = [1, 2, 3, 4, "A", "B"];

// 1. 取出 arr 中数字项为新数组
let numArr = [];
for (let i in arr) {
  if (typeof arr[i] === "number") {
    numArr.push(arr[i]);
  }
}

// 改用filter
let numArr2 = arr.filter(item => typeof item === "number");
console.log(numArr2); // [1,2,3,4]

// 2. 获得新数组，元素是 arr 每个元素作为 value, key 为 arr 下标的对象, 不修改 arr
let strArr = [];
for (let i in arr) {
  strArr.push({ [i]: arr[i] });
}
// 改用 map
let strArr2 = arr.map((item, i) => ({ [i]: arr[i] }));
console.log(strArr2); // [ { '0': 1 },{ '1': 2 },{ '2': 3 }, { '3': 4 }, { '4': 'A' }, { '5': 'B' } ]
```

#### 浅拷贝、深拷贝 复杂数据类型对象深拷贝建议使用库来实现，如 [lodash.cloneDeep](http://lodash.think2011.net/cloneDeep)

```js
// 浅拷贝
let obj1 = { a: 11, b: { ba: 22 } };
let obj2 = { ...obj1 };
console.log(obj2); // ​​​​​{ a: 11, b: { ba: 22 } }​​​​​

console.log(obj1 === obj2); // false
console.log(obj2.b === obj1.b); // true

// 深拷贝，这种方法需要对象能够被 json 序列化
let obj3 = JSON.parse(JSON.stringify(obj1));
console.log(obj3); //  ​​​​​{ a: 11, b: { ba: 22 } }​​​​​
console.log(obj3 === obj1); // false
console.log(obj3.b === obj1.b); // true
```

### 更高效的代码

#### 使用局部变量代替引用类型查找

局部变量的读取速度最快，而引用类型的数据读取需要按引用指针去查找，所以可以对多次使用的引用类型属性 使用局部变量读取一次，重复使用

```js
let obj = {
  person: {
    man: {
      bao: {
        age: 18
      }
    }
  }
};

let age = obj.person.man.bao.age;
// use age do many things
```

#### 删除多个对象属性时先使属性为 null

删除属性时，js 引擎会去查找该属性的值是否是其他对象的引用，所以删除前提前赋值为 null，可以减少 js 引擎的检测过程，提高效率

```js
let obj = {
  person: {
    man: {
      bao: {
        age: 18
      }
    }
  }
};
obj.person = null;
delete obj.person;
```
