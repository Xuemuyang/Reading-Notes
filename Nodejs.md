# Node.js

## 总结

### 关于模块

- 每个文件是一个模块，有自己的作用域
- 模块内部`module`变量代表模块本身
- `module.exports`属性代表模块对外接口
- 通过`module.exports`暴露模块里面的变量和方法

#### exports 与 module.exports 的区别

`exports`是对`module.exports`的一个全局引用。

`exports.myFunc`只是`module.exports.myFunc`的简写。

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

##### require 特性

- module 被加载的时候执行，加载后缓存(如果一个模块被加载多次，只有第一次加载的时候执行模块中语句)
- 一旦出现某个模块被循环加载(A,B 模块互相引用)，就只输出已经执行的部分，还未执行的部分不会输出

modA.js

```js
module.exports.test = "A";

const modB = require("./modB");
console.log("modA: ", modB.test);

module.exports.test = "AA";
```

modB.js

```js
module.exports.test = "B";

const modA = require("./modA");
console.log("modB: ", modA.test);

module.exports.test = "BB";
```

main.js

```js
const modA = require("./modA");

const modB = require("./modB");
```

terminal 打印

```bash
modB: A
modA: BB
```

### 关于路径

`path.resolve()`用来转换相对当前路径的绝对路径。

以下都是绝对路径

- `__dirname`: 获取当前执行文件所在目录的完整目录名
- `__filename`: 获得当前执行文件的带有完整绝对路径的文件名
- `process.cwd()`: 获得当前执行 node 命令时的文件夹目录名
- `path.resolve('./')`: 文件所在目录(与`process.cwd()`一致)

`require`模块的时候，Node 会在被执行程序文件(即调用`require`的文件)所在的目录下寻找这个模块。

在`require()`时采用相对路径，其他地方一律使用绝对路径。

## Node 简介

- 事件驱动
- 非阻塞 I/O

### Node 特点

#### 异步 I/O

Node 的结构与 Chrome 十分相似，都是基于事件驱动的异步架构，浏览器通过事件驱动来服务界面上的交互，Node 通过事件驱动来服务 I/O。

异步调用中对于结果值得捕获符合"Don't call me,I will call you"原则。

Node 中，异步 I/O 很常见，以读取文件为例，与前端 Ajax 调用的方式是及其类似的。

```javascript
var fs = require("fs");

fs.readFile("/path", function(err, file) {
  console.log("读取文件完成");
});
console.log("发起读取文件");
```

这里的“发起读取文件”是在“读取文件完成”之前输出的。“读取文件完成”的执行取决于读取文件的异步调用何时结束。

#### 事件与回调函数

事件的编程方式具有轻量级、松耦合、只关注事务点等优势。

回调函数无处不在，JavaScript 中函数作为一等公民。将函数作为对象传递给方法进行调用。

#### 单线程

单线程的弱点有 3 个方面

- 无法利用多核 CPU
- 错误会引起整个应用退出，应用的健壮性值得考验
- 大量计算占用 CPU 导致无法继续调用异步 I/O

## 模块机制

B/S 和 C/S

## Node 中的定时器

`Node`在`c++`层和`OS`中使用了`libuv`库处理回调函数执行。

`Node`提供了四个定时器。

- `setTimeout()`
- `setInterval()`
- `setImmediate()`
- `prcess.nextTick()`

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

`Promise`对象的回调函数会进入异步任务里面的"微任务"microtask 队列，这个队列追加在`process.nextTick`队列的后面。

> 只有前一个队列全部清空，才会执行下一个队列。

本轮循环的执行顺序为:

1. 同步任务
1. `process.nextTick()`即 nextTickQueue
1. `microtask`微任务

### Event Loop

[玄学](http://www.ruanyifeng.com/blog/2018/02/node-event-loop.html)

Node 开始开始执行脚本时，会先进行事件循环初始化，先做下面这些事:

- 同步任务
- 发出异步请求
- 规划定时器生效事件
- 执行`process.nextTick()`等等

事件循环的六个阶段

- timers
- I/O callbacks
- idle, prepare
- poll
- check
- close callbacks

  1.timers

定时器阶段，处理`setTimeOut()`和`setInterval()`的回调函数。

2.I/O callbacks

除了下列操作的回调函数，其他的回调函数都在这个阶段执行

- `setTimeOut()`和`setInterval()`的 callback
- `setImmediate()`的 callback
- 用于关闭请求的 callback

3.idle,prepare

libuv 内部调用

4.Poll

这个阶段用于等待还未返回的 I/O 事件，如果没有其他异步任务要处理，会一直停留在这个阶段

5.check

`setImmedeate()`的 callback

6.close callbacks

该阶段执行关闭请求的 callback

### 一个事件循环的示例

```js
const fs = require("fs");

const timeoutScheduled = Date.now();

// 异步任务一：100ms 后执行的定时器
setTimeout(() => {
  const delay = Date.now() - timeoutScheduled;
  console.log(`${delay}ms`);
}, 100);

// 异步任务二：文件读取后，有一个 200ms 的回调函数
fs.readFile("test.js", () => {
  const startCallback = Date.now();
  while (Date.now() - startCallback < 200) {
    // 什么也不做
  }
});
```

第一轮循环，没有到期的定时器以及可以执行的 I/O callback，进入 Poll 阶段(文件读取操作)，由于小文件一般不会超过 100ms，定时器到期之前，Poll 阶段就会有结果，继续执行。

第二轮事件循环，依旧没有到期的定时器，但是已经有了可执行的 I/O callback，这个函数需要 200ms，及执行一半不到的时候定时器就会到期，必须等到函数执行完毕才会离开这个阶段。

第三轮时间循环，timer 阶段定时器 callback 触发。

## 调试

第一种方式，使用 chrome

```sh
$node --inspect-brk xxx.js
```

-brk 是指在程序刚执行的时候就打上断点

之后在 chrome://inspect 进入调试

第二种使用 VScode

## Node 实战

第一部分 Node 基础

### 第 1 章 欢迎进入 Node.js 世界

- 构建在 JavaScript 之上
- 事件驱动和异步的
- 专为数据密集型实时程序设计的

Node包含了V8提供的语言特性，特性分为`shipping`、`staged`和`in progress`三组。其中`shipping`默认开启，`staged`需要使用`--harmony`开启。

Node的核心模块相当于其他语言的标准库。

#### 异步和事件触发:浏览器

浏览器中的`alert`,`prompt`,`confirm`,同步`XHR`会阻塞程序执行。

#### 异步和事件触发:服务器

Nginx 采用的就是带有异步 I/O 的事件轮询

#### DIRT 程序

DIRT(data-intensive real-time)数据密集型实时程序

文档在线协作，多人在线游戏

### 构建有多个房间的聊天室程序

为了提供静态文件，需要使用 Node 内置的 http 模块，通过 HTTP 提供文件时，通常不能只是发送文件中的内容，还应该有所发送文件的类型。也就是说要用正确的`MIME`类型设置 HTTP 头的`Content-Type`。

聊天程序需要具备三个基本功能:

- 给用户的 Web 浏览器提供静态文件
- 在服务端处理与聊天相关的消息
- 在用户的 Web 浏览器中处理与聊天相关的消息

访问内存(RAM)要比访问文件系统快，Node 程序通常会把常用的数据存到内存里，第一次访问的时候会从文件系统中读取，后面就会从缓存中读取。

### Node 编程基础

#### Node 模块

Node 模块化视为了重用，他们不会改变全局作用域。

Node 模块允许你从被引入文件中选择要暴露给程序的函数和变量。

```js
var canadianDollar = 0.91;

function roundTwoDecimals(amount) {
  return Math.round(amount * 100) / 100;
}

exports.canadianToUS = function(canadian) {
  return roundTwoDecimals(canadian * canadianDollar);
};

exports.USToCanadian = function(us) {
  return roundTwoDecimals(us / canadianDollar);
};
```

这个模块的 exports 对象上只设定了两个属性，引入这个模块的代码只能访问到`candianToUS`和`USToCanadian`这两个函数。而变量`canadianDollar`作为私有变量仅作用在`candianToUS`和`USToCanadian`的逻辑内部，程序不能直接访问它。

`require`是 Node 中少数几个同步 I/O 操作之一。

`require`模块的时候，Node 会在被执行程序文件所在的目录下寻找这个模块。

![require模块规则](./images/actionsinnode/1.jpeg)

#### 异步编程技术

Node 中比较常用的异步有

- callback
- 事件监听

事件监听本质上也是回调，它跟事件相关联。

每当服务器收到请求，就触发`handleRequest`这个函数。

```js
server.on("request", handleRequest);
```

callback hell 的例子

```js
var http = require("http");
var fs = require("fs");

http
  .createServer(function(req, res) {
    if (req.url == "/") {
      fs.readFile("./titles.json", function(err, data) {
        if (err) {
          console.error(err);
          res.end("Server Error");
        } else {
          var titles = JSON.parse(data.toString());

          fs.readFile("./template.html", function(err, data) {
            if (err) {
              console.error(err);
              res.end("Server Error");
            } else {
              var tmpl = data.toString();

              var html = tmpl.replace("%", titles.join("</li><li>"));
              res.writeHead(200, {
                "Content-Type": "text/html"
              });
              res.end(html);
            }
          });
        }
      });
    }
  })
  .listen(8000, "127.0.0.1");
```

calback 时代的 node 程序为了减少 callback 嵌套可以使用创建中间函数，以及尽早返回的方法。

```js
var http = require("http");
var fs = require("fs");

var server = http
  .createServer(function(req, res) {
    getTitles(res);
  })
  .listen(8000, "127.0.0.1");

function getTitles(res) {
  fs.readFile("./titles.json", function(err, data) {
    if (err) return hadError(err, res);
    getTemplate(JSON.parse(data.toString()), res);
  });
}

function getTemplate(titles, res) {
  fs.readFile("./template.html", function(err, data) {
    if (err) return hadError(err, res);
    formatHtml(titles, data.toString(), res);
  });
}

function formatHtml(titles, tmpl, res) {
  var html = tmpl.replace("%", titles.join("</li><li>"));
  res.writeHead(200, {
    "Content-Type": "text/html"
  });
  res.end(html);
}

function hadError(err, res) {
  console.error(err);
  res.end("Server Error");
}
```

##### 使用事件发射器处理重复性事件

```js
// 使用on监听事件
socket.on("data", function(data) {
  socket.write(data);
});

// 使用once方法响应单次事件
socket.once("data", function(data) {
  socket.write(data);
});
```

所有触发事件的对象都是`events`的实例。

```js
const EventEmitter = require("events");

class CustomEvent extends EventEmitter {}

const ce = new CustomEvent();

ce.on("error", (err, time) => {
  console.log(err);
  console.log(time);
});

ce.emit("error", new Error("opps!"), Date.now());
```

##### 扩展事件监听器:文件监视器

扩展事件发射器需要三步

- 创建类的构造器
- 继承事件发射器的行为
- 扩展这些行为

`inherits`函数是 Node 内置的`util`模块里的，使用`inherits`函数继承另一个对象里的行为非常简洁。

```js
function Watcher(watchDir, processedDir) {
  this.watchDir = watchDir;
  this.processedDir = processedDir;
}

var events = require("events"),
  util = require("util");

util.inherits(Watcher, events.EventEmitter);
```

#### 流程控制(异步逻辑的顺序化)

实现串行化流程控制时，需要跟踪当前执行的任务，或维护一个尚未执行的任务队列。实现并行化流程控制需要跟踪有多少个任务已经执行完成。

```js
var fs = require("fs");
var request = require("request");
var htmlparser = require("htmlparser");
var configFilename = "./rss_feeds.txt";

function checkForRSSFile() {
  fs.exists(configFilename, function(exists) {
    if (!exists) return next(new Error("Missing RSS file: " + configFilename));

    next(null, configFilename);
  });
}

function readRSSFile(configFilename) {
  fs.readFile(configFilename, function(err, feedList) {
    if (err) return next(err);

    feedList = feedList
      .toString()
      .replace(/^\s+|\s+$/g, "")
      .split("\n");
    var random = Math.floor(Math.random() * feedList.length);
    next(null, feedList[random]);
  });
}

function downloadRSSFeed(feedUrl) {
  request(
    {
      uri: feedUrl
    },
    function(err, res, body) {
      if (err) return next(err);
      if (res.statusCode != 200)
        return next(new Error("Abnormal response status code"));

      next(null, body);
    }
  );
}

function parseRSSFeed(rss) {
  var handler = new htmlparser.RssHandler();
  var parser = new htmlparser.Parser(handler);
  parser.parseComplete(rss);

  if (!handler.dom.items.length) return next(new Error("No RSS items found"));

  var item = handler.dom.items.shift();
  console.log(item.title);
  console.log(item.link);
}

var tasks = [checkForRSSFile, readRSSFile, downloadRSSFeed, parseRSSFeed];

function next(err, result) {
  if (err) throw err;

  var currentTask = tasks.shift();

  if (currentTask) {
    currentTask(result);
  }
}

next();
```

本例中串行化流程控制本质是在需要时让回调进场、而不是简单地把它们嵌套起来。

下面是并行化流程控制，程序会读取几个文本文件的内容，输出单词在整个文件中出现的次数。

```js
var fs = require("fs");
var completedTasks = 0;
var tasks = [];
var wordCounts = {};
var filesDir = "./text";

function checkIfComplete() {
  completedTasks++;
  if (completedTasks == tasks.length) {
    for (var index in wordCounts) {
      console.log(index + ": " + wordCounts[index]);
    }
  }
}

function countWordsInText(text) {
  var words = text
    .toString()
    .toLowerCase()
    .split(/\W+/)
    .sort();
  for (var index in words) {
    var word = words[index];
    if (word) {
      wordCounts[word] = wordCounts[word] ? wordCounts[word] + 1 : 1;
    }
  }
}

fs.readdir(filesDir, function(err, files) {
  if (err) throw err;
  for (var index in files) {
    var task = (function(file) {
      return function() {
        fs.readFile(file, function(err, text) {
          if (err) throw err;
          countWordsInText(text);
          checkIfComplete();
        });
      };
    })(filesDir + "/" + files[index]);
    tasks.push(task);
  }
  for (var task in tasks) {
    tasks[task]();
  }
});
```

### 第 4 章 构建 Node Web 程序

Node 的核心是一个强大的流式 HTTP 解析器。

Node 中的 http 模块提供了 HTTP 服务器和客户端接口。

这个函数只有一个参数，是个 callback，服务器每次收到 HTTP 请求后都会调用这个 callback。

```js
http.createServer([requestListener]);
```

```js
const http = require("http");

const server = http.createServer((req, res) => {
  // 处理请求
});
```

Node 不会自动往客户端写任何响应。需要调用`res.end()`方法结束响应，如果不结束，请求会挂起，直到客户端超时。

Node 提供了 HTTP 响应头的方法:

- res.setHeader(name, value)
- res.getHeader(name)
- res.removeHeader(name)

添加和移除响应头的顺序合一随意，但一定要在调用`res.write()`或`res.end()`之前。

通过`res.statusCode`设置响应码。

```js
let url = "http://google.com";

let body = '<p>Redirecting to <a href="' + url + '">' + url + "</a></p>";

res.setHeader("Location", url);

res.setHeader("Content-Length", body.length);

res.setHeader("Content-Type", "text/html");

res.statusCode = 302;

res.end(body);
```

#### 构建 RESTful Web 服务

创建、读取、更新和删除(CRUD)即增删改查

实现一个简单的 todolist

创建标准的 REST 服务器需要实现四个 HTTP 谓词，每个谓词会覆盖一个 todolist 的操作任务

- POST 向 todolist 中添加事项
- GET 显示当前事项列表，或者显示某一事项的详情
- DELETE 从 todolist 中移除事项
- PUT 修改已有事项

通过`req.method`属性查看用的是哪个 HTTP 方法，从而知道要执行哪个任务。

设定`Content-Length`头提高响应速度，`Content-Length`的值是字节长度，而不是字符长度，在 Node 中，英文字符占一个字节，中文字符占 3 个，Node 提供了`Bffer.byteLength()`方法规避这个问题。

```js
const http = require("http");
const url = require("url");
const items = []; // 用一个JS数组存放数据

const server = http.createServer((req, res) => {
  switch (req.method) {
    case "POST":
      let item = "";
      req.setEncoding("utf8");
      req.on("data", chunk => {
        item += chunk;
      });
      req.on("end", () => {
        items.push(item);
        res.end("OK\n");
      });
      break;
    case "GET":
      let body = items
        .map((item, i) => {
          return i + ") " + item;
        })
        .join("\n");
      res.setHeader("Content-Length", Buffer.byteLength(body));
      res.setHeader("Content-Type", 'text/plain; charset="utf-8"');
      res.end(body);
      break;
    case "DELETE":
      let path = url.parse(req.url).pathname;
      let i = parseInt(path.slice(i), 10);

      if (isNaN(i)) {
        res.statusCode = 400;
        res.end("Invalid item id");
      } else if (!items[i]) {
        res.statusCode = 404;
        res.end("Item not found");
      } else {
        items.splice(i, 1);
        res.end("OK\n");
      }
      break;
  }
});

server.listen(3000, () => {
  console.log("Server listening on port 3000.");
});
```

#### 静态文件服务

目录遍历攻击

应该防止用户通过目录遍历攻击访问到本来不想开放给他们的那部分内容。

使用流优化数据传输

```js
var server = http.createServer(function(req, res) {
  var url = parse(req.url);
  var path = join(root, url.pathname);
  fs.stat(path, function(err, stat) {
    if (err) {
      if ("ENOENT" == err.code) {
        res.statusCode = 404;
        res.end("Not Found");
      } else {
        res.statusCode = 500;
        res.end("Internal Server Error");
      }
    } else {
      res.setHeader("Content-Length", stat.size);
      var stream = fs.createReadStream(path);
      stream.pipe(res);
      stream.on("error", function(err) {
        res.statusCode = 500;
        res.end("Internal Server Error");
      });
    }
  });
});

server.listen(3000, () => {
  console.log("Server listening on port 3000.");
});
```

### 第 5 章 存储 Node 程序中的数据

#### 从表单接受用户输入

Node 一贯宗旨是提供简单高效的 API，把其他机会留给了社区。

表单提交请求带的`Content-Type`值通常有两种:

- application/x-www-form-urlencoded 这是 HTML 表单的默认值
- multipart/form-data 在表单中含有文件或非 ASCII 或二进制数据时使用

前端上传文件，需要把表单的`enctype`设置为`multipart/form-data`,这是个适用于 BLOB(大型二进制文件)的 MIME 类型。

选择合适的存储机制取决于以下五个因素：

- 存储什么数据
- 为了保证性能，要有多快的数据读取和写入速度
- 有多少数据
- 要怎么查询数据
- 数据要保存多久，对可靠性有什么要求

内存存储的理想用途是存放少量经常使用的数据。

DBMS(数据库管理系统)

RDMBS(关系型数据库管理系统)

ORM(Object Relational Mapping-对象关系映射)

SQL 语句中的?是用来指明应该把参数放在哪里的占位符。在添加到查询语句之前，query 方法会自动把参数转义，以防遭受到 SQL 注入攻击。

```js
exports.add = function(db, req, res) {
  exports.parseReceivedData(req, function(work) {
    db.query(
      "INSERT INTO work (hours, date, description) " + " VALUES (?, ?, ?)",
      [work.hours, work.date, work.description],
      function(err) {
        if (err) throw err;
        exports.show(db, res);
      }
    );
  });
};
```

#### NoSQL 数据库

##### Redis

Redis 适合处理那些不需要长期访问的简单数据存储。Redis 将数据存在 RAM 中，并在磁盘中记录数据的变化。缺点是存储空间有限，好处是操作数据非常快。如果 Redis 服务器崩溃，RAM 中的内容丢了，可以用磁盘中的日志回复数据。

### 第 6 章 Connect

```js
const connect = require("connect");
const app = connect();

app.listen(3000);
```

在 Connect 中，中间件组件是一个 JavaScript 函数，按惯例会接受三个参数：一个请求对象，一个响应对象，还有一个通常命名为 next 的参数，它是一个回调函数，表明这个组件已经完成了它的工作，可以执行下一个中间件组件了。

再加上两个中间件，日志中间件和 helloworld 中间件

```js
const connect = require("connect");

const logger = (req, res, next) => {
  console.log("%s %s", req.method, req.url);
  next();
};

const hello = (req, res) => {
  res.setHeader("Content-Type", "text/plain");
  res.end("Hello World");
};

connect()
  .use(logger)
  .use(hello)
  .listen(3000);
```

这个例子中的 hello 中间件没有`next`回调，因为这个组件结束了 HTTP 响应。

如果中间件不调用`next()`，控制权就不会被交回到分派器去调用下一个中间件组件。

下面这个例子用中间件来进行认证，防止没有登录的人访问某些内容。

```js
const connect = require("connect");

connect()
  .use(logger)
  .use(restrictFileAccess) // 只有用户有效时才会调用next()
  .use(hello)
  .listen(3000);
```

#### 创建可配置的中间件

为了向开发人员提供可配置的能力，中间件通常会遵循一个简单的惯例:用函数返回另一个函数

##### 构建路由中间件组件

在 Web 程序中，路由是个至关重要的概念。简言之，它会把请求 URL 映射到实现业务逻辑的函数上。

调用

```js
var connect = require("connect");
var router = require("./middleware/router");
var routes = {
  GET: {
    "/users": function(req, res) {
      res.end("tobi, loki, ferret");
    },
    "/user/:id": function(req, res, id) {
      res.end("user " + id);
    }
  },
  DELETE: {
    "/user/:id": function(req, res, id) {
      res.end("deleted user " + id);
    }
  }
};

connect()
  .use(router(routes))
  .listen(3000);
```

路由中间件

```js
var parse = require("url").parse;

module.exports = function route(obj) {
  return function(req, res, next) {
    if (!obj[req.method]) {
      // 检查确保req.method定义了
      next();
      return;
    }
    var routes = obj[req.method]; // 查找req.method对应的路径
    var url = parse(req.url); // 解析URL，以便跟pathname匹配
    var paths = Object.keys(routes); // 将req.method对应的路径存放到数组中

    for (var i = 0; i < paths.length; i++) {
      var path = paths[i];
      var fn = routes[path];
      path = path.replace(/\//g, "\\/").replace(/:(\w+)/g, "([^\\/]+)");
      var re = new RegExp("^" + path + "$");
      var captures = url.pathname.match(re);
      if (captures) {
        var args = [req, res].concat(captures.slice(1));
        fn.apply(null, args);
        return;
      }
    }
    next();
  };
};
```

构建中间件应该关注那些小型的，可配置的部分。构建大量微小的、模块化的、可重用的中间件组合，合起来搭成程序。

##### 使用错误处理中间件

```js
var connect = require("connect");

function badMiddleware(req, res, next) {
  next(new Error("Bad middleware makes error"));
}

function errorHandler() {
  var env = process.env.NODE_ENV || "development";
  return function(err, req, res, next) {
    res.statusCode = 500;
    switch (env) {
      case "development":
        res.setHeader("Content-Type", "application/json");
        res.end(JSON.stringify(err));
        break;
      default:
        res.end("Server error");
    }
  };
}

connect()
  .use(badMiddleware)
  .use(errorHandler)
  .listen(3000);
```

### 第 7 章 Connect 自带的中间件

1.`cookieParser()`:解析 HTTP cookie

2.`bodyParser()`:解析请求主体

3.`limit()`:请求主题的限制

如果将`limit()`设定为 32kb，会看到 Connect 请求到 32kb 时候终止请求

4.`query()`:查询参数解析

5.`logger()`:请求记录

可以在这个中间件定制日志的格式

6.`favicon()`:提供 favicon

7.`methodOverride()`:伪造 HTTP 方法

`<form>`只能`GET`或`POST`，在 HTML 中返回一个隐藏的元素，通过这个元素的 value 来修改请求的方法

```js
var connect = require("connect");

function edit(req, res, next) {
  if ("GET" != req.method) return next();
  res.setHeader("Content-Type", "text/html");
  res.write('<form method="post">');
  res.write('<input type="hidden" name="_method" value="put" />');
  res.write('<input type="text" name="user[name]" value="Tobi" />');
  res.write('<input type="submit" value="Update" />');
  res.write("</form>");
  res.end();
}

function update(req, res, next) {
  if ("PUT" != req.method) return next();
  res.end("Updated name to " + req.body.user.name);
}

var app = connect()
  .use(connect.logger("dev"))
  .use(connect.bodyParser())
  .use(connect.methodOverride())
  .use(edit)
  .use(update)
  .listen(3000);
```

8.`vhost()`:虚拟主机

`vhost()`(虚拟主机)中间件是一种通过请求头 Host 路由请求的简单、轻量的办法。这项任务通常是由反向代理完成的，可以把请求转发到运行在不同端口上的本地服务器那里。

9.`session`:会话管理

- 设定会话有效期
- 处理会话数据
- 操纵会话 cookie
- 会话存储

10.`basicAuth()`:HTTP 基本认证

11.`csrf()`:跨站请求伪造(CSRF)保护

12.`errorHandle()`:开发错误处理

这个中间件在开发阶段使用，放在最后，捕获所有错误，仅在开发阶段使用。

13.`static()`:静态文件服务

14.`compress()`:压缩静态文件

15.`directory()`:目录列表

### Express

#### 配置

Express 构建在 Connect 之上

可以安装全局的 express 模块来搭建项目目录

`express -e hehe` 这里的`-e`代表使用 ejs 模板引擎渲染，命令行使用`express -h`查看更多命令

在系统中设置环境变量

```sh
NODE_ENV=production node app
```

Express提供了一套方法来操作环境相关的配置

- app.set()
- app.get()
- app.enable()
- app.disable()
- app.enabled()
- app.disabled()

#### 视图渲染

设置模板引擎

```js
// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');
```

上面代码设置了模板的扩展名为`.ejs`，调用`render`函数的时候就可以省略扩展名

```js
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});
```

express 默认开启视图缓存

视图查找也有一套相应的规则

![](https://ws1.sinaimg.cn/large/006tNc79ly1fzu8f4y2gcj30pa0na10i.jpg)

## Koa

### 常用的中间件

koa-middlewares这个包中包含如下常用的模块

- koa-bodyparser
- koa-compress
- koa-conditional-get
- koa-csrf
- koa-ejs
- koa-etag
- koa-favicon
- koa-generic-session
- koa-logger
- koa-onerror
- koa-redis
- koa-resource-router
- koa-rewrite
- koa-router
- koa-rt
- koa-safe-jsonp
- koa-session
- koa-static-cache

### 阮老师的 Intro

Koa 的特点是优雅、简洁、表达力强、自由度高，本身代码只有 1000+行，所有功能通过插件实现，符合 Unix 哲学。

Koa 对于 Node 版本有要求。

#### 基本用法

启动 HTTP 服务

```js
const Koa = require("koa");
const app = new Koa();

app.listen(3000);
```

Koa 提供了一个 Context 对象，表示一次对话的上下文(包括 HTTP 请求和 HTTP 回复)。通过加工这个对象，可以控制返回给用户的内容。

`Context.response.body`属性就是发送给用户的内容。

```js
const Koa = require("koa");
const app = new Koa();

const main = ctx => {
  ctx.response.body = "Hello World";
};

app.use(main);
app.listen(3000);
```

Koa 默认返回类型是`text/plain`，使用`ctx.request.accepts`判断客户端希望接收什么数据。

```js
const Koa = require("koa");
const app = new Koa();

const main = ctx => {
  if (ctx.request.accepts("xml")) {
    ctx.response.type = "xml";
    ctx.response.body = "<data>Hello World</data>";
  } else if (ctx.request.accepts("json")) {
    ctx.response.type = "json";
    ctx.response.body = { data: "Hello World" };
  } else if (ctx.request.accepts("html")) {
    ctx.response.type = "html";
    ctx.response.body = "<p>Hello World</p>";
  } else {
    ctx.response.type = "text";
    ctx.response.body = "Hello World";
  }
};

app.use(main);
app.listen(3000);
```

#### 路由

`ctx.request.path`获取用户请求的路径，实现简单的路由。

```js
const Koa = require("koa");
const app = new Koa();

const main = ctx => {
  if (ctx.request.path !== "/") {
    ctx.response.type = "html";
    ctx.response.body = '<a href="/">Index Page</a>';
  } else {
    ctx.response.body = "Hello World";
  }
};

app.use(main);
app.listen(3000);
```

原生路由用起来不够方便，可以使用封装好的`koa-router`模块。

这段代码中，根路径`/`的处理函数是`main`，`/about`路径的处理函数是`about`。

```js
const Koa = require("koa");
const route = require("koa-route");
const app = new Koa();

const about = ctx => {
  ctx.response.type = "html";
  ctx.response.body = '<a href="/">Index Page</a>';
};

const main = ctx => {
  ctx.response.body = "Hello World";
};

app.use(route.get("/", main));
app.use(route.get("/about", about));

app.listen(3000);
```

使用`koa-static`实现静态资源服务

```js
const Koa = require("koa");
const app = new Koa();
const path = require("path");
const serve = require("koa-static");

const main = serve(path.join(__dirname));

app.use(main);
app.listen(3000);
```

`ctx.response.redirect()`实现 302 重定向

```js
const Koa = require("koa");
const route = require("koa-route");
const app = new Koa();

const redirect = ctx => {
  ctx.response.redirect("/");
  ctx.response.body = '<a href="/">Index Page</a>';
};

const main = ctx => {
  ctx.response.body = "Hello World";
};

app.use(route.get("/", main));
app.use(route.get("/redirect", redirect));

app.use(main);
app.listen(3000);
```

#### 中间件(middleware)

看一个 Logger 的例子

```js
const Koa = require("koa");
const app = new Koa();

const logger = (ctx, next) => {
  console.log(`${Date.now()} ${ctx.request.method} ${ctx.request.url}`);
  next();
};

const main = ctx => {
  ctx.response.body = "Hello World";
};

app.use(logger);
app.use(main);
app.listen(3000);
```

这里的`logger`函数就是中间件，因为它处在 HTTP Request 和 HTTP Response 之间实现某种中间功能。使用`app.use()`来加载中间件。

基本上 Koa 所有的功能都是通过中间件来实现的，每个中间件默认接受两个参数，`Context`对象和`next`函数。

#### 中间件栈

1. 最外层的中间件首先执行。
1. 调用 next 函数，把执行权交给下一个中间件。
1. ...
1. 最内层的中间件最后执行。
1. 执行结束后，把执行权交回上一层的中间件。
1. ...
1. 最外层的中间件收回执行权之后，执行 next 函数后面的代码。

```js
const Koa = require("koa");
const app = new Koa();

const one = (ctx, next) => {
  console.log(">> one");
  next();
  console.log("<< one");
};

const two = (ctx, next) => {
  console.log(">> two");
  next();
  console.log("<< two");
};

const three = (ctx, next) => {
  console.log(">> three");
  next();
  console.log("<< three");
};

app.use(one);
app.use(two);
app.use(three);

app.listen(3000);
```

输出

```js
>> one
>> two
>> three
<< three
<< two
<< one
```

中间件中包含异步操作，必须写成`async`函数

```js
const main = async function(ctx, next) {
  ctx.response.type = "html";
  ctx.response.body = await fs.readFile("./demos/template.html", "utf8");
};
```

`koa-compose`模块可以将多个中间件合成一个

```js
const logger = (ctx, next) => {
  console.log(`${Date.now()} ${ctx.request.method} ${ctx.request.url}`);
  next();
  console.log("logger");
};

const main = ctx => {
  console.log("main");
  ctx.response.body = "Hello World";
};

const middlewares = compose([logger, main]);
```

#### 错误处理

Koa 提供了`ctx.throw()`方法来抛出错误，`ctx.throw(500)`就是抛出 500 错误。

```js
const main = ctx => {
  ctx.throw(500);
};
```

将`ctx.response.status`设置成 404 相当于`ctx.throw(404)`。

```js
const main = ctx => {
  ctx.response.status = 404;
  ctx.response.body = "Page Not Found";
};
```

错误处理的中间件

```js
const handler = async (ctx, next) => {
  try {
    await next();
  } catch (err) {
    ctx.response.status = err.statusCode || err.status || 500;
    ctx.response.body = {
      message: err.message
    };
  }
};

const main = ctx => {
  ctx.throw(500);
};

app.use(handler);
app.use(main);
```

运行过程出错，Koa 会触发一个`error`事件。监听这个事件也可以处理错误。

```js
const main = ctx => {
  ctx.throw(500);
};

app.on("error", (err, ctx) => {
  console.error("server error", err);
});
```

如果错误被`try...catch`捕获就不会触发`error`事件，这时必须调用`ctx.app.emit()`，手动触发`error`事件，才能让监听函数生效。

```js
const app = new Koa();

const handler = async (ctx, next) => {
  try {
    await next();
  } catch (err) {
    ctx.response.status = err.statusCode || err.status || 500;
    ctx.response.type = "html";
    ctx.response.body = "<p>Something wrong, please contact administrator.</p>";
    ctx.app.emit("error", err, ctx);
  }
};

const main = ctx => {
  ctx.throw(500);
};

app.on("error", function(err) {
  console.log("logging error ", err.message);
  console.log(err);
});
```

#### Web App 的功能

- `ctx.cookies`实现读写 Cookie
- `koa-body`模块可以用来提取请求的 key-value，以及文件上传等功能
