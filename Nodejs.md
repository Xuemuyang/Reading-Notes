# Node.js

## Node简介

+ 事件驱动
+ 非阻塞I/O

### Node特点

#### 异步I/O

Node的结构与Chrome十分相似，都是基于事件驱动的异步架构，浏览器通过事件驱动来服务界面上的交互，Node通过事件驱动来服务I/O。

异步调用中对于结果值得捕获符合"Don't call me,I will call you"原则。

Node中，异步I/O很常见，以读取文件为例，与前端Ajax调用的方式是及其类似的。

```javascript
var fs = require('fs');

fs.readFile('/path', function(err, file) {
    console.log('读取文件完成');
});
console.log('发起读取文件');
```

这里的“发起读取文件”是在“读取文件完成”之前输出的。“读取文件完成”的执行取决于读取文件的异步调用何时结束。

#### 事件与回调函数

事件的编程方式具有轻量级、松耦合、只关注事务点等优势。

回调函数无处不在，JavaScript中函数作为一等公民。将函数作为对象传递给方法进行调用。

#### 单线程

单线程的弱点有3个方面

+ 无法利用多核CPU
+ 错误会引起整个应用退出，应用的健壮性值得考验
+ 大量计算占用CPU导致无法继续调用异步I/O

## 模块机制

B/S和C/S

## Node中的定时器

`Node`在`c++`层和`OS`中使用了`libuv`库处理回调函数执行。

`Node`提供了四个定时器。

+ `setTimeout()`
+ `setInterval()`
+ `setImmediate()`
+ `prcess.nextTick()`

先看一个小例子

```js
// test.js
setTimeout(() => console.log(1));
setImmediate(() => console.log(2));
process.nextTick(() => console.log(3));
Promise.resolve().then(() => console.log(4));
(() => console.log(5))();
```

```js
$ node test.js
5
3
4
1
2
```

### 本轮循环和次轮循环

`process.nextTick`和`Promise`的回调函数追加在本轮循环。

`Node`执行完所有同步任务，就会执行`process.nextTick`的任务队列。

`Promise`对象的回调函数会进入异步任务里面的microtask队列。

> 只有前一个队列全部清空，才会执行下一个队列。

本轮循环的执行顺序为:

1. 同步任务
1. `process.nextTick()`
1. 微任务

## Node实战

第一部分 Node基础

### 欢迎进入Node.js世界

+ 构建在JavaScript之上
+ 事件驱动和异步的
+ 专为数据密集型实时程序设计的

#### 异步和事件触发:浏览器

浏览器中的`alert`,`prompt`,`confirm`,同步`XHR`会阻塞程序执行。

#### 异步和事件触发:服务器

Nginx采用的就是带有异步I/O的事件轮询

#### DIRT程序

DIRT(data-intensive real-time)数据密集型实时程序

文档在线协作，多人在线游戏