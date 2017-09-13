# Node入门

## 一个基础的HTTP服务器

``` javascript
var http = require('http');

http.createServer(function(request, response) {
	response.writeHead(200, {"Content-Type": "text/plain"});
	response.write('Hello World');
	response.end();
}).listen(8888);
```

第一行请求（require）Node.js自带的 http 模块，并且把它赋值给 http 变量。

接下来我们调用http模块提供的函数： createServer 。这个函数会返回一个对象，这个对象有一个叫做 listen 的方法，这个方法有一个数值参数，指定这个HTTP服务器监听的端口号。

命名为`server.js`

cli下`node server.js`启动

> 当我们在服务器访问网页时，我们的服务器可能会输出两次“Request received.”。那是因为大部分浏览器都会在你访问 http://localhost:8888/ 时尝试读取 http://localhost:8888/favicon.ico

**基于事件驱动的回调**

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

把我们的服务器脚本放到一个叫做`start`的函数里，然后我们会导出这个函数。

创建主文件`index.js`

```javascript
var server = require('./server');

server.start();
```

### 路由模块

index.js
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


