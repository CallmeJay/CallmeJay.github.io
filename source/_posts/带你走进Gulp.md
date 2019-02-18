---
title: 带你走进Gulp
date: 2017-07-11 23:05:47
categories: gulp
tags: gulp
---

`Gulp`是一款前端自动化构建工具。通俗的来说，就是你本来需要很多条指令才可以完成的事情，通过`Gulp`你可能只需要一条指令。

<!--more-->

### Gulp 是什么鬼

首先，我很确定它不是鬼。对于之前接触过`Webpack`的人来说，很大的一个疑惑可能就是：这两者有什么区别？因为`Webpack`几乎能做全部我们需要做的事情，`Gulp`存在的意义是什么？我只能说：存在即合理。
`Gulp`貌似是一款取代`Grunt`的工具，我没有去了解过`Grunt`，可能是因为没时间，而且好像也不是很有必要。`Gulp`的核心功能是**自动化任务流程，简化操作**。比如你需要做打包、编译、压缩、合并等一些乱七八糟的事情，不用`Gulp`，你可能需要一步一步的“自己”来，而有了`Gulp`，你预先将这些乱七八糟的事情定义好交给`Gulp`，然后在特定时刻，`Gulp`自动将这些事情全都做了。
而`Webpack`的核心功能应该是集中在模块管理上，不过它同样可以通过各种插件，做很多事情。过于纠结这两者的区别也没什么必要。`Gulp`简单，学起来也就一两天的事。`Webpack`相对要复杂很多，而且对于新手也有很多坑，但不可忽略它确实很强大。这里的建议是，都学一下，毕竟这是现在最火的两款开发工具。

### 很重要的几个概念

`Gulp`不算复杂，去[官网](http://www.gulpjs.com.cn/)也就那么一丁点儿的介绍，所以学习之前有必要先知道一些概念。

#### 配置文件（gulpfile.js）

`Gulp`既然是一款工具，就得做一些事情，具体要做些什么，还得由我们来指定。在哪里指定，就是在它的配置文件`gulpfile.js`中，该文件位于项目的根目录下。以后所有`Gulp`相关的配置语句都是写在`gulpfile.js`文件中。

#### 任务（Task）

`Gulp`把需要做的一些事情定义成一个个的任务，每一个任务都有一个名字，然后通过命令调用这个任务的名字，就可以执行该任务对应的代码。如下：

```js
// gulpfile.js 文件中

var gulp = require("gulp");

// style 任务
gulp.task("style", function() {
  // 一些要做的事情
});

// build 任务
gulp.task("build", function() {
  // 要做的事情
});
```

#### 插件

前面定义的都是一些空任务，啥也不会做。因为`Gulp`本身不会做任何“事情”，它只负责把那些要做的事情准备（分类、串联）好。具体谁来做，那就是插件。

`Gulp`有很多插件，几乎能满足你的各种需求。我想对于初学者来说有个疑问：我怎么知道我需要的插件叫什么？那么，我在这里先列出一些插件。我也不知道这些是不是常用的插件，不过看起来好像比较常用。
\*gulp-connect：可以跑一个本地服务器，貌似现在应该不会有人直接将 index.html 拖到浏览器来调试吧；

- `gulp-concat`：看清了，这是 concat，用来合并文件的；
- `gulp-clean`：删除文件用的；
- `gulp-rename`：重命名文件；
- `gulp-minify-html`：压缩 html 用的；
- `gulp-inject`：可以将 css 样式和 js 脚本文件插入到 html 文件中；
- `gulp-minify-css`：压缩 css；
- `gulp-uglify`：压缩 js；
- `gulp-imagemin`：压缩图片；
- `gulp-postcss`：自动添加 css 中的前缀，很实用的家伙。

就这些吧，看清楚长啥样就好了。正如你看到的那样，`Gulp`所有的插件都是以`gulp-`开头。对于那些有点经验的人来说，要什么插件可能一般都是直接去`npm`或`github`上搜索。对于不知道怎么搜的（一般都是不知道关键词），就老老实实百度吧。
一个很好的习惯就是**平时多记录**和**总结**，因为很多插件在不同的工具和平台中，叫法基本上都是一样的。另外，这里还给一个官方收录的[插件列表](https://gulpjs.com/plugins/)。

#### 具体使用方法

`Gulp`的安装就不说了，需要的直接看看[这里](http://www.gulpjs.com.cn/docs/getting-started/)。
先举一个栗子（玩玩）：

```js
// 引入gulp，无需解释
var gulp = require('gulp');
// 引入插件
var connect = require('gulp-connect')，
    sass = require('gulp-sass')，
    uglify = require('gulp-uglify');

// 定义任务
gulp.task('server', function () {
    // 使用 connect 插件创建一个服务器
    connect.server({
        // 这里是传入的一些配置项
        root: 'dist',
        livereload: true
    })
});

gulp.task('style', function () {
    // 匹配资源文件
    gulp.src('./src/**/*.scss')
    // 对匹配到的 文件 传到一个插件中
    .pipe(sass({    // 用sass插件处理 匹配到的文件
        // 插件的配置项
        outputStyle: 'compressed'
        // 插件的功能 打印错误信息
    }).on('error', sass.logError))
    // 串联 将sass处理后的文件 传送到下一个插件中
    // 将文件 输出到指定目录下
    .pipe(gulp.dest('dist'))
    // 重新加载 服务器
    .pipe(connect.reload());
});

gulp.task('script', function () {
    gulp.src('./src/**/*.js')
    .pipe(uglify())
    .pipe(gulp.dest('dist'))
    .pipe(connect.reload());
});

// 默认任务 要等到数组中的任务执行完之后 才执行
gulp.task('default', ['style', 'script', 'server'], function () {
    // 监听 匹配到的文件是否改变
    // 一旦变化就执行 数组 中的任务
    gulp.watch('./src/js/*.js', ['script']);
    gulp.watch('./src/css/*.scss', ['style']);
});
```

`Gulp`的基本使用大概就这些，`gulpfile.js`文件大概就是这个样子的。对于该配置文件，命令行只需执行一条语句：

```
gulp
```

没错，就这样！该指令会自动执行**默认的任务**。如果要指定具体的任务，后面直接跟上任务名即可，比如：

```
gulp server
```

#### 如何使用插件

一般典型的用法就是直接将插件传入`.pipe`函数中，插件的配置项通过一个对象传入插件中：

```
.pipe(uglify())
```

一般若没有特殊要求，可以省略配置项。`gulp.src()`和`.pipe()`都会返回一个叫`Vinyl files`的`stream`（我也不知道是个什么鬼），所以这些方法是可以串联起来的。

#### 谈几个 API

这里列出一些比较常用的 API 接口，详细用法请参阅[官方文档](http://www.gulpjs.com.cn/docs/api/)

- `gulp.src`：匹配文件用的；
- `gulp.dest`：输出文件到指定目录；
- `gulp.task`：定义一个任务；
- `gulp.watch`：监听文件，改动触发。

好像就这四个？我也是刚发现，还说列几个常用的。所以学起来还是很容易的，不过要全部学通，可能也要花一点时间。反正我是没有认真把官网的文档看完，尽管它一点也不长。我的想法是，掌握基本的使用方法和思想，有实际需求的时候再去参考官方文档。（关键是我现在没这反面的需求啊）

#### 文件匹配原则

好像这家伙不是按照常规的正则去匹配的，官方给出了一个[参考](https://github.com/isaacs/node-glob)，有兴趣的自己看看，反正我没看。
直接举几个栗子吧：

- `*`：能匹配`abc.js`，`a.css`，不能匹配`a/bc.js`；
- `**`：能匹配`a.js`，`a/b/cd/d.css`；
- `*.*`：能匹配`aaa.js`，`b.css`；
- `a/**/*.js`：能匹配`a/b/c/d/ef.js`，`a/b/c.js`。

还要说明一点，语句`gulp.dest('dist')`中的`dist`表示在项目**根目录**下生成`dist`目录。输出的文件路径是`*`号匹配到的文件名（未被修改的情况下），上面例子中输出的目录结构是这样的：

```
// 假设原文件路径
- src
    - css
        - a.scss
    - js
        - b.js

// 输出的文件路径
- dist
    - css
        - a.css
    - js
        - b.js
```

默认情况下，`dist`只会替换`*`号之前的路径，当然也可以自己修改，具体自行参阅[官方文档](http://www.gulpjs.com.cn/docs/api/)

> [传送门](http://www.jianshu.com/p/081a3de6be93)
