# Node入门

## 目标

+ 通过浏览器使用
+ 访问/start，欢迎页面，文件上传表单
+ 用户选择图片并提交表单，文件被上传到/upload，上传完成后会把图片显示在页面上。

### 实现目标分析

+ 一个HTTP服务器
+ 根据请求的URL，服务器做出不同的响应，需要一个路由，用于把请求对应到请求处理程序(request handler)
+ 当请求被服务器接收并通过路由传递后，需要事件处理程序进行处理
+ 路由能够处理POST数据，并且把数据封装成更友好的格式传入事件处理程序
+ 将返回内容发送给用户的浏览器
+ 用户需要上传图片

## 一个基础的HTTP服务器

保持代码分离，需要有一个主文件，通常称为`index.js`

``` javascript
var http = require('http');

http.createServer(function(request, response) {
  response.writeHead(200, {"Content-Type": "text/plain"});
  response.write('Hello World');
  response.end();
}).listen(8888);
```

第一行请求（require）Node.js自带的`http`模块，并且把它赋值给`http`变量。

接下来我们调用http模块提供的函数：`createServer`。这个函数会返回一个对象，这个对象有一个叫做`listen`的方法，这个方法有一个数值参数，指定这个HTTP服务器监听的端口号。

**基于事件驱动的回调**

无论何时我们的服务器收到一个请求，这个函数就会被调用。

命名为`server.js`

cli下`node server.js`启动

> 当我们在服务器访问网页时，我们的服务器可能会输出两次“Request received.”。那是因为大部分浏览器都会在你访问 http://localhost:8888/ 时尝试读取 http://localhost:8888/favicon.ico

``` javascript
var http = require("http");

function start() {
  function onRequest(request, response) {
    console.log("Request received.");
    response.writeHead(200, {"Content-Type": "text/plain"});
    response.write("Hello World");
    response.end();
  }

  http.createServer(onRequest).listen(8888);
  console.log("Server has started.");
}

exports.start = start;
```

当收到请求时，使用`response.writeHead()`发送一个`HTTP`状态`200`和`HTTP`头的内容类型(content-type)，使用`response.write()`函数在`HTTP`相应主体中发送文本"Hello World"。最后使用`response.end()`完成响应。

把我们的服务器脚本放到一个叫做`start`的函数里，然后我们会导出这个函数。

创建主文件`index.js`

```javascript
var server = require('./server');

server.start();
```

### 路由模块

![](http://ovyn493ey.bkt.clouddn.com/nodeurl.png)

为了解析`url`需要额外的Node.JS模块，`url`和`querystring`模块

使用路由将请求以URL路径为基准**映射**到处理程序上

server.js
```javascript
var http = require("http");
var url = require("url");

function start(route) {
  function onRequest(request, response) {
    
    var pathname = url.parse(request.url).pathname;
    console.log("Request for " + pathname + " received.");

    route(pathname);

    response.writeHead(200, {"Content-Type": "text/plain"});
    response.write("Hello World");
    response.end();
  }

  http.createServer(onRequest).listen(8888);
  console.log("Server has started.");
}

exports.start = start;
```

route.js

```javascript
function route(pathname) {
  console.log("About to route a request for" + pathname);
}

exports.route = route;
```

为什么传递的是函数而不是对象，在对象中调用方法也可以达到一样的效果，然而只需要路由这个动作，不需要名词。这就是函数式编程的基本思想。

### requestHandlers模块

```javascript
function start() {
  console.log("Request handler 'start' was called.");
}

function upload() {
  console.log("Request handler 'upload' was called.");
}

exports.start = start;
exports.upload = upload;
```

都引入到主文件index.js

```javascript
var server = require("./server");
var router = require("./router");
var requestHandlers = require("./requestHandlers");

var handle = {};
handle['/'] = requestHandlers.start;
handle['/start'] = requestHandlers.start;
handle['/upload'] = requestHandlers.upload;

server.start(router.route, handle);
```

将handle对象传给服务器，对server.js做出修改
```javascript
var http = require("http");
var url = require("url");

function start(route, handle) {
  function onRequest(request, response) {
    var pathname = url.parse(request.url).pathname;
    console.log("Request for " + pathname + " received.");

    route(handle, pathname);

    response.writeHead(200, {"Content-Type": "text/plain"});
    response.write("Hello World");
    response.end();
  }

  http.createServer(onRequest).listen(8888);
  console.log("Server has started.");
}

exports.start = start;
```

引入handle，同样在route.js中修改`route()`函数

```javascript
function route(handle, pathname, response) {
  console.log("About to route a request for " + pathname);
  if (typeof handle[pathname] === 'function') {
    handle[pathname]();
  } else {
    console.log("No request handler found for " + pathname);
  }
}

exports.route = route;
```

### 以非阻塞操作进行请求响应

通过(回调)函数传递

```javascript
var http = require("http");
var url = require("url");

function start(route, handle) {
  function onRequest(request, response) {
    var pathname = url.parse(request.url).pathname;
    console.log("Request for " + pathname + " received.");

    route(handle, pathname, response);
  }

  http.createServer(onRequest).listen(8888);
  console.log("Server has started.");
}

exports.start = start;
```

router.js
```javascript
function route(handle, pathname, response) {
  console.log("About to route a request for " + pathname);
  if (typeof handle[pathname] === 'function') {
    handle[pathname](response);
  } else {
    console.log("No request handler found for " + pathname);
    response.writeHead(404, {"Content-Type": "text/plain"});
    response.write("404 Not found");
    response.end();
  }
}

exports.route = route;
```

requestHandler.js
```javascript
var exec = require("child_process").exec;

function start(response) {
  console.log("Request handler 'start' was called.");

  exec("ls -lah", function (error, stdout, stderr) {
    response.writeHead(200, {"Content-Type": "text/plain"});
    response.write(stdout);
    response.end();
  });
}

function upload(response) {
  console.log("Request handler 'upload' was called.");
  response.writeHead(200, {"Content-Type": "text/plain"});
  response.write("Hello Upload");
  response.end();
}

exports.start = start;
exports.upload = upload;
```

上述代码中引入了一个新的Node.js模块，child_process，为了实现一个非阻塞操作:`exec()`。它从Node.js来执行一个shell命令。我们用它来获取当前目录下所有文件，之后将信息返回到浏览器中。

### 实战

#### 处理POST请求

实现思路：将data和end事件的回调函数直接放在服务器中，在data事件回调中收集所有的POST数据，当接收到所有数据，触发end实践后，其回调函数调用请求路由，并将数据传递给它，然后请求路由再将该数据传递给请求处理程序。

server.js
```javascript
var http = require("http");
var url = require("url");
function start(route, handle) {
  function onRequest(request, response) {
    var postData = "";
    var pathname = url.parse(request.url).pathname;
    console.log("Request for " + pathname + " received.");
    request.setEncoding("utf8");
    request.addListener("data", function(postDataChunk) {
      postData += postDataChunk;
      console.log("Received POST data chunk '"+
      postDataChunk + "'.");
});
    request.addListener("end", function() {
      route(handle, pathname, response, postData);
}); }
  http.createServer(onRequest).listen(8888);
  console.log("Server has started.");
}
exports.start = start;
```

router.js
```javascript
function route(handle, pathname, response, postData) {
  console.log("About to route a request for " + pathname);
  if (typeof handle[pathname] === 'function') {
    handle[pathname](response, postData);
  } else {
    console.log("No request handler found for " + pathname);
    response.writeHead(404, {"Content-Type": "text/plain"});
    response.write("404 Not found");
    response.end();
} }
exports.route = route;
```

requestHandler.js
```javascript
var querystring = require("querystring");
function start(response, postData) {
  console.log("Request handler 'start' was called.");
  var body = '<html>'+
    '<head>'+
    '<meta http-equiv="Content-Type" content="text/html; '+
    'charset=UTF-8" />'+
    '</head>'+
    '<body>'+
    '<form action="/upload" method="post">'+
    '<textarea name="text" rows="20" cols="60"></textarea>'+
    '<input type="submit" value="Submit text" />'+
    '</form>'+
    '</body>'+
    '</html>';
    response.writeHead(200, {"Content-Type": "text/html"});
    response.write(body);
    response.end();
}
function upload(response, postData) {
  console.log("Request handler 'upload' was called.");
  response.writeHead(200, {"Content-Type": "text/plain"});
  response.write("You've sent the text: "+
  querystring.parse(postData).text);
  response.end();
}
exports.start = start;
exports.upload = upload;
```
这里使用querystring模块来拿到postData中的text字段

#### 文件上传