---
title: 学习react
date: 2019-05-29 10:44:39
tags: react
---

> 组件的挂载指的是将组件渲染并且构造 DOM 元素然后插入页面的过程。这是一个从无到有的过程，React.js 提供一些生命周期函数可以给我们在这个过程中做一些操作。

<!-- more -->

### virtual-dom

virtual-dom也就是使用js的数据结构来表示dom元素的结构，因为不是真是的dom节点，也就称为虚拟DOM。它最大的特点是将页面进行抽象成JS对象形式，配合不同的工具使跨平台成为可能，可以根据不同的平台渲染出相应的真实“DOM”。除此之外，在页面进行更新的时候，可以将DOM元素的变动放在内存比较，再结合一些框架的机制，将多次的渲染合并成一次渲染更新。

挂载过程：

```text
-> constructor()
-> componentWillMount()
-> render()
// 然后构造 DOM 元素插入页面
-> componentDidMount()
// ...
// 组件将要接收新的props执行 这个方法在初始化render时不会被调用。
-> componentWillReceiveProps(nextProps)

// 判断组件是否应该重新渲染，默认是true 在组件接收到新的props或者state时被调用。在初始化时或者使用forceUpdate时不被调用。
-> shouldComponentUpdate(nextProps, nextState)

// 组件将要重新渲染 在组件接收到新的props或者state但还没有render时被调用。在初始化时不会被调用。 不能调用setState。造成死循环，导致程序崩溃。
-> componentWillUpdate(nextProps, nextState)

// 组件重新渲染完成 不能调用setState。造成死循环，导致程序崩溃。
-> componentDidUpdate(prevProps, prevState, snapshot)

// ...
// 即将从页面中删除
-> componentWillUnmount()
// 从页面中删除
```

![React生命周期](https://user-images.githubusercontent.com/19704583/61278476-db980300-a7e6-11e9-974a-1c62001c3dce.jpeg)

> 组件的私有方法都用 `_` 开头，所有事件监听的方法都用 `handle` 开头。把事件监听方法传给组件的时候，属性名用 `on` 开头。

### 组件的内容编写顺序如下

1. static 开头的类属性，如 `defaultProps`、`propTypes`。
2. 构造函数，`constructor`。
3. getter/setter（还不了解的同学可以暂时忽略）。
4. 组件生命周期。
5. `_` 开头的私有方法。
6. 事件监听方法，`handle*`。
7. `render*`开头的方法，有时候 `render()` 方法里面的内容会分开到不同函数里面进行，这些函数都以 `render*` 开头。
8. `render()` 方法。

### 什么是高阶组件（Higher-Order Component）

> 高阶组件就是一个函数，传给它一个组件，它返回一个新的组件。新的组件使用传入的组件作为子组件。

作用：高阶组件的作用是用于代码复用，可以把组件之间可复用的代码、逻辑抽离到高阶组件当中。新的组件和传入的组件通过 props 传递信息。

### 纯函数（Pure Function）

> 一个函数的返回结果只依赖于它的参数，并且在执行过程里面没有副作用

除了修改外部的变量，一个函数在执行过程中还有很多方式产生外部可观察的变化，比如说调用 DOM API 修改页面，或者你发送了 Ajax 请求，还有调用 window.reload 刷新浏览器，甚至是 console.log 往控制台打印数据也是副作用。

纯函数很严格，也就是说你几乎除了计算数据以外什么都不能干，计算的时候还不能依赖除了函数参数以外的数据。

为什么要煞费苦心地构建纯函数？因为纯函数非常“靠谱”，执行一个纯函数你不用担心它会干什么坏事，它不会产生不可预料的行为，也不会对外部产生影响。不管何时何地，你给它什么它就会乖乖地吐出什么。如果你的应用程序大多数函数都是由纯函数组成，那么你的程序测试、调试起来会非常方便。

### PureComponent

React.PureComponent 与 React.Component 几乎完全相同，但 React.PureComponent 通过props和state的浅对比来实现 shouldComponentUpate()。如果对象包含复杂的数据结构，它可能会因深层的数据不一致而产生错误的否定判断(表现为对象深层的数据已改变视图却没有更新）

> 关注点

* 无论组件是否是 PureComponent，如果定义了 shouldComponentUpdate()，那么会调用它并以它的执行结果来判断是否 update。在组件未定义 shouldComponentUpdate() 的情况下，会判断该组件是否是 PureComponent，如果是的话，会对新旧 props、state 进行 shallowEqual 比较，一旦新旧不一致，会触发 update。
* 浅判断只会比较到两个对象的ownProperty是否符合 `Object.is()` 判等，不会递归地去深层比较。
* 至于复杂数据结构，用Object.key()获取下key，然后key和对应的value都是基础类型数据，就是算是简单数据结构，不然就是复杂

针对以上规则我们在项目开发种可以做出如下优化：

> 尽量将复杂类型数据（ArrayList）所关联的视图单独拆成PureComonent有助于提高渲染性能，比如表单、文本域和复杂列表在同一个 render() 中，表单域的输入字段改变会频繁地触发 setState() 从而导致 组件 重新 render()。而用于渲染复杂列表的数据其实并没有变化，但由于重新触发 render()，列表还是会重新渲染。

### 为什么要手动绑定this

通过事件触发过程的分析，dispatchEvent调用了invokeGuardedCallback方法。

```js
function invokeGuardedCallback(name, func, a) {
  try {
    func(a);
  } catch (x) {
    if (caughtError === null) {
      caughtError = x;
    }
  }
}
```

可见，回调函数是直接调用调用的，并没有指定调用的组件，所以不进行手动绑定的情况下直接获取到的`this`是`undefined`。
这里可以使用实验性的属性初始化语法 ，也就是直接在组件声明箭头函数。箭头函数不会创建自己的`this`，它只会从自己的作用域链的上一层继承`this`。因此这样我们在`React`事件中获取到的就是组件本身了。

### React事件和原生事件的执行顺序

```jsx
componentDidMount() {
  this.parent.addEventListener('click', (e) => {
    console.log('dom parent');
  })
  document.addEventListener('click', (e) => {
    console.log('document');
  })
}

parentClick = (e) => {
  console.log('react parent');
}

render() {
  return (
    <div onClick={this.parentClick} ref={ref => this.parent = ref}>
      test
    </div>
  )
}


// dom parent
// react parent
// document
```

由上面的流程我们可以理解：

* `react`的所有事件都挂载在`document`中
* 当真实dom触发后冒泡到`document`后才会对`react`事件进行处理
* 所以原生的事件会先执行
* 然后执行`react`合成事件
* 最后执行真正在`document`上挂载的事件

### React事件和原生事件可以混用吗

react事件和原生事件最好不要混用。

原生事件中如果执行了`stopPropagation`方法，则会导致其他react事件失效。因为所有元素的事件将无法冒泡到`document`上。

由上面的执行机制不难得出，所有的react事件都将无法被注册。

### 基于路由的代码分割

```js
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';
import React, { Suspense, lazy } from 'react';

const Home = lazy(() => import('./routes/Home'));
const About = lazy(() => import('./routes/About'));

const App = () => (
  <Router>
    <Suspense fallback={<div>Loading...</div>}>
      <Switch>
        <Route exact path="/" component={Home}/>
        <Route path="/about" component={About}/>
      </Switch>
    </Suspense>
  </Router>
);
```

### 用户定义的组件必须以大写字母开头

以小写字母开头的元素代表一个 HTML 内置组件，比如 `<div>` 或者 `<span>` 会生成相应的字符串 `'div'` 或者 `'span'` 传递给 `React.createElement`（作为参数）。大写字母开头的元素则对应着在 JavaScript 引入或自定义的组件，如 `<Foo />` 会编译为 `React.createElement(Foo)`。

```jsx
import React from 'react';
import { PhotoStory, VideoStory } from './stories';

const components = {
  photo: PhotoStory,
  video: VideoStory
};

function Story(props) {
  // 正确！JSX 类型可以是大写字母开头的变量。
  const SpecificStory = components[props.storyType];
  return <SpecificStory story={props.story} />;
}
```

### 字符串字面量

```jsx
<MyComponent message="hello world" />
<MyComponent message={'hello world'} />
```
