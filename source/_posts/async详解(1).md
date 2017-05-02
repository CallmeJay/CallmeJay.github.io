---
title: async详解之流程控制
date: 2017-05-02 11:11:21
categories: Nodejs
tags: 
    - async
    - node
---
*“流程控制”本来是件比较简单的事，但是由于Nodejs的异步架构的实现方法，对于需要同步的业务逻辑，实现起来就比较麻烦。嵌套3-4层，代码就会变得的支离破碎了！*
## **Async介绍**
Async是一个流程控制工具包，提供了直接而强大的异步功能。基于Javascript为Node.js设计，同时也可以直接在浏览器中使用。
Async提供了大约20个函数，包括常用的 map, reduce, filter, forEach 等，异步流程控制模式包括，串行(series)，并行(parallel)，瀑布(waterfall)等。
<!--more-->
## **安装与调用**
  **安装**
```
npm install --save async
```
  **调用**
```
var async = require("async");
// 或者
var waterfall = require("async/waterfall");
```
## **使用**
*由于nodejs是异步编程模型，有一些在同步编程中很容易做到的事情，现在却变得很麻烦。Async的流程控制就是为了简化这些操作。*
### series(tasks, [callback]) （多个函数依次执行，之间没有数据交换）
有多个异步函数需要依次调用，一个完成之后才能执行下一个。各函数之间没有数据的交换，仅仅需要保证其执行顺序。这时可使用series。
纯js代码：
```
step1(function(err, v1) {
  step2(function(err, v2) {
    step3(function(err, v3) {
       // do somethig with the err or values v1/v2/v3
    }
  }
});
```
从中可以看到这嵌套还是比较多深的，如果再多几步，会更深。在代码中忽略对了每一层err的处理，否则还都等加上 if(err) return callback(err)，那就更麻烦了。
对于这种情况，使用async来处理，就是这样的：
```
// 数组形式
async.series([
    function(callback) {
        // do some stuff ...
        callback(null, 'one');
    },
    function(callback) {
        // do some more stuff ...
        callback(null, 'two');
    }
],
// optional callback
function(err, results) {
    // results is now equal to ['one', 'two']
});

// json形式（可读性更高）
async.series({
    one: function(callback) {
        setTimeout(function() {
            callback(null, 1);
        }, 200);
    },
    two: function(callback){
        setTimeout(function() {
            callback(null, 2);
        }, 100);
    }
}, function(err, results) {
    // results is now equal to: {one: 1, two: 2}
});
```
**该函数的详细解释为：**
  1. 依次执行一个函数数组中的每个函数，每一个函数执行完成之后才能执行下一个函数。
  2. 如果任何一个函数向它的回调函数中传了一个error，则后面的函数都不会被执行，并且将会立刻会将该error以及已经执行了的函数的结果，传给series中最后那个callback。
  3. 当所有的函数执行完后（没有出错），则会把每个函数传给其回调函数的结果合并为一个数组，传给series最后的那个callback。
  4. 还可以json的形式来提供tasks。每一个属性都会被当作函数来执行，并且结果也会以json形式传给series最后的那个callback。这种方式可读性更高一些。
*另外还需要注意的是：多个series调用之间是不分先后的，因为series本身也是异步调用。*
[**详细示例**](https://github.com/freewind/async_demo/blob/master/series.js)

### parallel(tasks, [callback]) （多个函数并行执行）
并行执行多个函数，每个函数都是立即执行，不需要等待其它函数先执行。传给最终callback的数组中的数据按照tasks中声明的顺序，而不是执行完成的顺序。
如果某个函数出错，则立刻将err和已经执行完的函数的结果值传给parallel最终的callback。其它未执行完的函数的值不会传到最终数据，但要占个位置。
同时支持json形式的tasks，其最终callback的结果也为json形式。
示例代码：
```
// 数组形式
async.parallel([
    function(callback) {
        setTimeout(function() {
            callback(null, 'one');
        }, 200);
    },
    function(callback) {
        setTimeout(function() {
            callback(null, 'two');
        }, 100);
    }
],
// optional callback
function(err, results) {
    // the results array will equal ['one','two'] even though
    // the second function had a shorter timeout.
});

// an example using an object instead of an array
async.parallel({
    one: function(callback) {
        setTimeout(function() {
            callback(null, 1);
        }, 200);
    },
    two: function(callback) {
        setTimeout(function() {
            callback(null, 2);
        }, 100);
    }
}, function(err, results) {
    // results is now equals to: {one: 1, two: 2}
});
```
[**详细示例**](https://github.com/freewind/async_demo/blob/master/parallel.js)

### waterfall(tasks, [callback]) （多个函数依次执行，且前一个的输出为后一个的输入）
与seires相似，按顺序依次执行多个函数。不同之处，每一个函数产生的值，都将传给下一个函数。如果中途出错，后面的函数将不会被执行。错误信息以及之前产生的结果，将传给waterfall最终的callback。
这个函数名为waterfall(瀑布)，可以想像瀑布从上到下，中途冲过一层层突起的石头。注意，该函数不支持json格式的tasks。
```
async.waterfall([
    function(callback) {
        callback(null, 'one', 'two');
    },
    function(arg1, arg2, callback) {
        // arg1 now equals 'one' and arg2 now equals 'two'
        callback(null, 'three');
    },
    function(arg1, callback) {
        // arg1 now equals 'three'
        callback(null, 'done');
    }
], function (err, result) {
    // result now equals 'done'
});

// Or, with named functions:
async.waterfall([
    myFirstFunction,
    mySecondFunction,
    myLastFunction,
], function (err, result) {
    // result now equals 'done'
});

function myFirstFunction(callback) {
    callback(null, 'one', 'two');
}
function mySecondFunction(arg1, arg2, callback) {
    // arg1 now equals 'one' and arg2 now equals 'two'
    callback(null, 'three');
}
function myLastFunction(arg1, callback) {
    // arg1 now equals 'three'
    callback(null, 'done');
}
```
[**详细示例**](https://github.com/freewind/async_demo/blob/master/waterfall.js)

###  auto(tasks, [callback]) （多个函数有依赖关系，有的并行执行，有的依次执行）

用来处理有依赖关系的多个任务的执行。比如某些任务之间彼此独立，可以并行执行；但某些任务依赖于其它某些任务，只能等那些任务完成后才能执行。
虽然我们可以使用async.parallel和async.series结合起来实现该功能，但如果任务之间关系复杂，则代码会相当复杂，以后如果想添加一个新任务，也会很麻烦。这时使用async.auto，则会事半功倍。
如果有任务中途出错，则会把该错误传给最终callback，所有任务（包括已经执行完的）产生的数据将被忽略。
这里假设我要写一个程序，它要完成以下几件事：
1. 从某处取得数据
2. 在硬盘上建立一个新的目录
3. 将数据写入到目录下某文件
4. 发送邮件，将文件以附件形式发送给其它人。
分析该任务，可以知道1与2可以并行执行，3需要等1和2完成，4要等3完成。
```
async.auto({
    get_data: function(callback) {
        console.log('in get_data');
        // async code to get some data
        callback(null, 'data', 'converted to array');
    },
    make_folder: function(callback) {
        console.log('in make_folder');
        // async code to create a directory to store a file in
        // this is run at the same time as getting the data
        callback(null, 'folder');
    },
    write_file: ['get_data', 'make_folder', function(results, callback) {
        console.log('in write_file', JSON.stringify(results));
        // once there is some data and the directory exists,
        // write the data to a file in the directory
        callback(null, 'filename');
    }],
    email_link: ['write_file', function(results, callback) {
        console.log('in email_link', JSON.stringify(results));
        // once the file is written let's email a link to it...
        // results.write_file contains the filename returned by write_file.
        callback(null, {'file':results.write_file, 'email':'user@example.com'});
    }]
}, function(err, results) {
    console.log('err = ', err);
    console.log('results = ', results); // -> { makeFolder: undefined,
                                        //      getData: undefined,
                                        //      writeFile: 'myfile',
                                        //      emailFiles: 'myfile' }
});
```
[**详细示例**](https://github.com/freewind/async_demo/blob/master/auto.js)
