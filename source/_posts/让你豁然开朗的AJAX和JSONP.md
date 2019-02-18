---
title: 让你豁然开朗的AJAX和JSONP
date: 2017-07-07 22:25:58
categories: JavaScript
tags: ajax
---

### 先说说 JSONP 是怎么产生的：

1、一个众所周知的问题，`Ajax`直接请求普通文件存在跨域无权限访问的问题，甭管你是静态页面、动态网页、`web`服务、`WCF`，只要是跨域请求，一律不准；

2、不过我们又发现，Web 页面上调用 js 文件时则不受是否跨域的影响（不仅如此，我们还发现凡是拥有 src 这个属性的标签都拥有跨域的能力，比如`<script>`、`<img>`、`<iframe>`）；

<!--more-->

3、于是可以判断，当前阶段如果想通过纯`web`端（`ActiveX`控件、服务端代理、属于未来的`HTML5`之`Websocket`等方式不算）跨域访问数据就只有一种可能，那就是在远程服务器上设法把数据装进 js 格式的文件里，供客户端调用和进一步处理；

4、恰巧我们已经知道有一种叫做`JSON`的纯字符数据格式可以简洁的描述复杂数据，更妙的是`JSON`还被`js`原生支持，所以在客户端几乎可以随心所欲的处理这种格式的数据；

5、这样子解决方案就呼之欲出了，`web`客户端通过与调用脚本一模一样的方式，来调用跨域服务器上动态生成的`js`格式文件（一般以`JSON`为后缀），显而易见，服务器之所以要动态生成`JSON`文件，目的就在于把客户端需要的数据装入进去。

6、客户端在对`JSON`文件调用成功之后，也就获得了自己所需的数据，剩下的就是按照自己需求进行处理和展现了，这种获取远程数据的方式看起来非常像`AJAX`，但其实并不一样。

7、**为了便于客户端使用数据，逐渐形成了一种非正式传输协议，人们把它称作`JSONP`**，该协议的一个要点就是允许用户传递一个`callback`参数给服务端，然后服务端返回数据时会将这个`callback`参数作为函数名来包裹住`JSON`数据，这样客户端就可以随意定制自己的函数来自动处理返回数据了。

### JSONP 的客户端具体实现：

1.我们知道，哪怕跨域`js`文件中的代码（当然指符合`web`脚本安全策略的），`web`页面也是可以无条件执行的。
假设远程服务器上有一个`remote.js`文件，可以使用以下方式调用：

```js
//remote.js
alert("我是远程文件");
```

```html
//jsonp.html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>addScript</title>
    <script src="remote.js"></script>
  </head>
  <body></body>
</html>
```

运行以后可以看到一个弹出框：我是远程文件，说明跨域调用成功。

2.现在我们在`jsonp.html`页面定义一个函数，然后在远程`remote.js`中传入数据进行调用。

```html
//jsonp.html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>addScript</title>
    <script type="text/javascript">
      let localHandler = function(data) {
        alert(
          "我是本地函数，可以被跨域的remote.js文件调用，远程js带来的数据是：" +
            data.result
        );
      };
    </script>
    <script src="remote.js"></script>
  </head>
  <body></body>
</html>
```

```js
//remote.js
localHandler({ result: "我是远程js带来的数据" });
```

运行之后，页面成功弹出提示窗口，显示本地函数被跨域的远程`js`调用成功，并且还接收到了远程`js`带来的数据。很欣喜，跨域远程获取数据的目的基本实现了，但是又一个问题出现了，我怎么让远程`js`知道它应该调用的本地函数叫什么名字呢？毕竟是`json`p 的服务者都要面对很多服务对象，而这些服务对象各自的本地函数都不相同啊？我们接着往下看。

3、聪明的开发者很容易想到，只要服务端提供的`js`脚本是动态生成的就行了呗，这样调用者可以传一个参数过去告诉服务端“我想要一段调用 XXX 函数的`js`代码，请你返回给我”，于是服务器就可以按照客户端的需求来生成`js`脚本并响应了。

```html
//jsonp.html
<!DOCTYPE html>
<head>
<html lang="en">
<title></title>
<script type="text/javascript"> // 得到航班信息查询结果后的回调函数
  let flightHandler = function(data){
    alert('你查询的航班结果是：票价 '+data.price + ' 元，'+'余票 ' + data.tickets + ' 张。');
  };
// 提供jsonp服务的url地址（不管是什么类型的地址，最终生成的返回值都是一段javascript代码）

let url = "flightResult.aspx?code=CA1998&callback=flightHandler";
// 创建script标签，设置其属性

let script = document.createElement('script');
script.setAttribute('src', url); // 把script标签加入head，此时调用开始
document.getElementsByTagName('head')[0].appendChild(script); </script>
</head>
<body></body>
</html>
```

这次的代码变化比较大，不再直接把远程`js`文件写死，而是编码实现动态查询，而这也正是`jsonp`客户端实现的核心部分，本例中的重点也就在于如何完成`jsonp`调用的全过程。

我们看到调用的`url`中传递了一个`code`参数，告诉服务器我要查的是 CA1998 次航班的信息，而`callback`参数则告诉服务器，我的本地回调函数叫做`flightHandler`，所以请把查询结果传入这个函数中进行调用。

OK，服务器很聪明，这个叫做`flightResult.js`的页面生成了一段这样的代码提供给`jsonp.html`（服务端的实现这里就不演示了，与你选用的语言无关，说到底就是拼接字符串）：

```js
flightHandler({ code: "CA1998", price: 1780, tickets: 5 });
```

我们看到，传递给`flightHandler`函数的是一个`json`，它描述了航班的基本信息。运行一下页面，成功弹出提示窗口，`jsonp`的执行全过程顺利完成！

4、到这里为止的话，相信你已经能够理解`jsonp`的客户端实现原理了吧？剩下的就是如何把代码封装一下，以便于与用户界面交互，从而实现多次和重复调用。

什么？你用的是`jQuery`，想知道`jQuery`如何实现`jsonp`调用？好吧，那我就好人做到底，再给你一段`jQuery`使用`jsonp`的代码（我们依然沿用上面那个航班信息查询的例子，假定返回`jsonp`结果不变）：

```html
<!DOCTYPE html>
<head>
<title>Untitled Page</title>
<script type="text/javascript" src="jquery.min.js"></script>
<script type="text/javascript">
    jQuery(document).ready(function(){
       $.ajax({
          type: "get",
          async: false,
          url: "remote.js?code=CA1998",
          dataType: "jsonp",
          jsonp: "callback",//传递给请求处理程序或页面的，用以获得jsonp回调函数名的参数名(一般默认为:callback)
          jsonpCallback:"flightHandler",//自定义的jsonp回调函数名称，默认为jQuery自动生成的随机函数名，也可以写"?"，jQuery会自动为你处理数据
          success: function(json){
            alert('您查询到航班信息：票价： ' + json.price + ' 元，余票： ' + json.tickets + ' 张。');
          },
          error: function(){
            alert('fail');
         }
       });
   });
</script>
</head>
<body></body>
</html>
```

是不是有点奇怪？为什么我这次没有写`flightHandler`这个函数呢？而且竟然也运行成功了！哈哈，这就是`jQuery`的功劳了，`jquery`在处理`jsonp`类型的`ajax`时（还是忍不住吐槽，虽然`jquery`也把`jsonp`归入了`ajax`，但其实它们真的不是一回事儿），自动帮你生成回调函数并把数据取出来供`success`属性方法来调用，是不是很爽呀？

### 补充：

---

这里针对`ajax`与`jsonp`的异同再做一些补充说明：
1、`ajax`和`jsonp`这两种技术在调用方式上“看起来”很像，目的也一样，都是请求一个`url`，然后把服务器返回的数据进行处理，因此`jquery`和`ext`等框架都把`jsonp`作为`ajax`的一种形式进行了封装；

2、但`ajax`和`jsonp`其实本质上是不同的东西。`ajax`的核心是通过`XmlHttpRequest`获取非本页内容，而`jsonp`的核心则是动态添加`<script>`标签来调用服务器提供的 js 脚本。

3、所以说，其实`ajax`与`jsonp`的区别不在于是否跨域，`ajax`通过服务端代理一样可以实现跨域，`jsonp`本身也不排斥同域的数据的获取。

4、还有就是，`jsonp`是一种方式或者说非强制性协议，如同`ajax`一样，它也不一定非要用`json`格式来传递数据，如果你愿意，字符串都行，只不过这样不利于用`jsonp`提供公开服务。

总而言之，`jsonp`不是`ajax`的一个特例，哪怕`jquery`等巨头把`jsonp`封装进了`ajax`，也不能改变着一点！

[传送门~](http://www.jianshu.com/p/3e6b316cc9a2)
