---
title: Browsersync的安装及使用方法
date: 2017-04-27 16:34:28
categories: tools
tags:
    - Browsersync
    - gulp
---
## Browsersync介绍
*Browsersync是浏览器同步测试工具，Browsersync能让浏览器实时、快速响应文件更改（html、js、css、sass、less等）并自动刷新页面。省去手动F5的事件，更重要的是 Browsersync可以同时在PC、平板、手机等设备下进项调试。即在任何一设备上操作，其他设备也随之改变，大大提高了测试效率。效果图：*
<!--more-->
<div align=center>
<img src="../images/browersync.gif" width = "400" height = "300" alt="自动刷新" />
</div>

## Browsersync安装
*Browsersync是基于node.js的，所以首先确定安装了node.js。*
(1) **Global Install（全局安装）**
*如果你想在任何目录的命令行中运行Browsersync ，可通过global命令进行全局安装。*
```
npm install -g browser-sync
```
(2) **Local Install （本地安装）**
推荐这种方式来安装 Browsersync - 通过本地安装到每个项目。这种方式的可以使依赖被添加到你的package.json文件里（gulp或grunt构建方式）
```
npm install browser-sync --save-dev
```
## 启动BrowerSync
*如果你只需要将css文件修改后同步到浏览器里，只需要在命令行里输入即可*
**静态网站**
```
// --files 路径是相对于运行该命令的项目（目录） 
browser-sync start --server --files "css/*.css"
```
监听多个类型的文件，需要用逗号隔开。例如我们在加入一个.html文件。
```
//--files 路径是相对于运行该命令的项目(目录)
brower-sync start --server --files "css/*.css,*.html"
// 如果你的文件层级比较深，您可以考虑使用 **（表示任意目录）匹配，任意目录下任意.css 或 .html文件。 
browser-sync start --server --files "**/*.css, **/*.html"
```
*运行命令后，Browsersync将创建一个本地服务器并自动打开你的浏览器后访问http://localhost:3000地址，这一切都会在命令行工具里显示。*
**动态网站**
*如果你已经有其他本地服务器环境PHP或类似的，需要使用代理模式。 BrowserSync将通过代理URL(localhost:3000)来查看。*
```
// 主机名可以是ip或域名
browser-sync start --proxy "ip/域名" "css/*.css"
// 比如：
browser-sync start --proxy "localhost:8081" "css/*.css"
```
## Browersync + gulp
**安装**
```
npm install -g --save-dev gulp browser-sync
```
**gulpfile.js代码**
**静态服务**
```
//加载gulp模块
var gulp = require('gulp');
//加载browser-sync模块
var browserSync = require('browser-sync').creat();
var reload = browserSync.reload;
//加载sass模块
var sass = require('gulp-sass');
//加载pug模块
var pug = require('gulp-pug');
//加载gulp-autoprefixer模块
var autoprefixer = require('gulp-autoprefixer');


/**
 * 这里静态服务器 + 监听 scss/pug/js 文件
 */
gulp.task('server',['sass','pug'],function(){
    browserSync.init({
        server:'./', //这里指的是根目录，如果你的index.html在根目录下，会直接打开index页面，不然会显示Get Not，自己写路径就行
        port:8081  //默认打开localhost:3000,现在改成localhost:8081
    });
    
    //监听 scss/pug/js 文件
    gulp.watch('sass/**/*.scss',['sass']);
    gulp.watch('jade/**/*.pug',['pug']);
    gulp.watch('page/**/*.js').on('change',reload);
});

/**
 * 编译sass
 */
gulp.task('sass',function(){
    return gulp.src('sass/**/*.scss')
        .pipe(sass().on('error', plugins.sass.logError))
        .pipe(autoprefixer('last 2 version', 'safari 5', 'ie 8', 'ie 9',       'opera 12.1', 'ios 6', 'android 4')) //这个插件是用来加浏览器前缀的
        .pipe(gulp.dest('./page'))
        .pipe(reload({stream:true}));
});

/**
 * 编译pug
 */
gulp.task('pug',function(){
    return gulp.src('jade/**/*.pug')
        .pipe(pug({pretty:true}))
        .pipe(gulp.dest('./page'))
        .pipe(reload({stream:true}));
});
/**
 * 默认运行
 */
gulp.task('default',['server']);
```
**动态服务**
*上面的例子中只涉及到了静态服务，但是项目中肯定是要把服务跑起来的，所以就要用到代理服务proxy*
```
/**
 * 代理服务器 + 监听 scss/pug/js 文件
 */
gulp.task('server',['sass','pug'],function(){
    browserSync.init({
        proxy:'http://localhost:8081/court-digital-library-search/page/portal/portalHomepage.html',
        port:8083
    });
    gulp.watch('sass/**/*.scss',['sass']);
    gulp.watch('jade/**/*.pug',['pug']);
    gulp.watch(['page/**/*.js','lar-ui/**/*.js']).on('change',reload);
});
```
*我们项目使用tomcat跑起来的，原本tomcat的访问地址是哪里，proxy指向哪里，browser-sync会生成一个新的带自动刷新的地址。*
**运行**
```
gulp
```