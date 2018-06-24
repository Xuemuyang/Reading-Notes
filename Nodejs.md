# Node.js

## 总结

### 关于模块

+ 每个文件是一个模块，有自己的作用域
+ 模块内部`module`变量代表模块本身
+ `module.exports`属性代表模块对外接口
+ 通过`module.exports`暴露模块里面的变量和方法

#### exports与module.exports的区别

可以理解`exports`是`module.exports`的一个快捷方式。

`exports`用字面量输出的时候会重写`exports`，此时`exports`与`module.exports`没有任何关系。

使用`module.exports`导出没有任何问题。

#### require

引入规则:

当 Node 遇到 require(X) 时，按下面的顺序处理。

1.如果 X 是内置模块（比如 require('http'）)

    a. 返回该模块。
    b. 不再继续执行。

2.如果 X 以 "./" 或者 "/" 或者 "../" 开头

    a. 根据 X 所在的父模块，确定 X 的绝对路径。
    b. 将 X 当成文件，依次查找下面文件，只要其中有一个存在，就返回该文件，不再继续执行。
        X
        X.js
        X.json
        X.node
    c. 将 X 当成目录，依次查找下面文件，只要其中有一个存在，就返回该文件，不再继续执行。
        X/package.json（main字段）
        X/index.js
        X/index.json
        X/index.node

3.如果 X 不带路径

    a. 根据 X 所在的父模块，确定 X 可能的安装目录。
    b. 依次在每个目录中，将 X 当成文件名或目录名加载。

4.抛出 "not found"

##### require特性

+ module被加载的时候执行，加载后缓存(如果一个模块被加载多次，只有第一次加载的时候执行模块中语句)
+ 一旦出现某个模块被循环加载(A,B模块互相引用)，就只输出已经执行的部分，还未执行的部分不会输出

modA.js

```js
module.exports.test = 'A';

const modB = require('./modB');
console.log('modA: ', modB.test);

module.exports.test = 'AA';
```

modB.js

```js
module.exports.test = 'B';

const modA = require('./modA');
console.log('modB: ', modA.test);

module.exports.test = 'BB';
```

main.js

```js
const modA = require('./modA');

const modB = require('./modB');
```

terminal打印

```bash
modB: A
modA: BB
```

### 关于路径

`path.resolve()`用来转换相对当前路径的绝对路径。

以下都是绝对路径

+ `__dirname`: 获取当前执行文件所在目录的完整目录名
+ `__filename`: 获得当前执行文件的带有完整绝对路径的文件名
+ `process.cwd()`: 获得当前执行node命令时的文件夹目录名
+ `path.resolve('./')`: 文件所在目录(与`process.cwd()`一致)

在`require()`时采用相对路径，其他地方一律使用绝对路径。

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

### 第1章 欢迎进入Node.js世界

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

### 第2章 构建有多个房间的聊天室程序

为了提供静态文件，需要使用Node内置的http模块，通过HTTP提供文件时，通常不能只是发送文件中的内容，还应该有所发送文件的类型。也就是说要用正确的`MIME`类型设置HTTP头的`COntent-Type`。

聊天程序需要具备三个基本功能:

+ 给用户的Web浏览器提供静态文件
+ 在服务端处理与聊天相关的消息
+ 在用户的Web浏览器中处理与聊天相关的消息

访问内存(RAM)要比访问文件系统快，Node程序通常会把常用的数据存到内存里，第一次访问的时候会从文件系统中读取，后面就会从缓存中读取。

### 第3章 Node编程基础

Node模块