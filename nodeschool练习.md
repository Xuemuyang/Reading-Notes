# nodeschool

## 1. 你好，世界

编写一个程序，在终端（标准输出 stdout）打印出 "HELLO WORLD"。

### 提示

要编写一个 Node.js 程序，你只需要创建一个后缀名为 .js 的文件，然后用
JavaScript 去编写即可。完成后，你可以通过命令行 node 命令来运行它，例如：

```
$ node program.js
```

和浏览器一样，你可以在你的 Node.js 程序中书写 console:

```javascript
console.log("text")
```

---
参考答案:

```javascript
console.log('HELLO WORLD')
```

## 2. 婴儿学步

编写一个简单的程序，使其能接收一个或者多个命令行参数，并且在终端（标准输出
stdout）中打印出这些参数的总和。

### 提示

你可以通过`process`这个全局对象来获取命令行中的参数。`process`对象拥有一个名为`argv`的属性，该属性是一个数组，数组中包含了整条命令的所有部分。

首先，请先编写一个包含如下带简易代码的程序来熟悉一下：

```js
console.log(process.argv)
```

通过执行命令`node program.js`并在后面多加几个参数，来运行我们的程序，比如:

```
$ node program.js 1 2 3
```

这样，你就会得到这样一个数组：

```js
[ 'node', '/path/to/your/program.js', '1', '2', '3' ]
```

你需要考虑的问题是，如何去循环遍历这些代表数字的参数，从而得到他们的总和。`process.argv`数组的第一个元素永远都会是`node`，并且第二个参数总是指向你的程序的路径，所以，你应该从第三个元素（index是2）开始，依次累加，直到数组末尾。

同时，你需要注意的是，所有`process.argv`中的数组的元素都是字符串类型的，因此，你需要将它们强制转换成数字。你可以通过加上`+`前缀，或者将其传给`Number()`来解决。例如:`+process.argv[2]`或者`Number(process.argv[2])`。

---
参考答案:

```js
var result = 0

    for (var i = 2; i < process.argv.length; i++) {
      result += Number(process.argv[i])
    }

console.log(result)
```

## 3. 第一个 I/O

编写一个程序，执行一个同步的文件系统操作，读取一个文件，并且在终端（标准输出 stdout）打印出这个文件中的内容的行(\n)数。类似于执行`cat file | wc -l`这个命令。

所要读取的文件的完整路径会在命令行第一个参数提供。

### 提示:

要执行一个对文件系统的操作，你将会用到 fs 这个 Node
核心模块。要加载这类核心模块，或者其他的"全局"模块，可以用下面的方式引入：

```js
var fs = require('fs')
```

现在你可以通过 fs 这个变量来访问整个 fs 模块了。

在 fs 中，所有的同步（或者阻塞）的操作文件系统的方法名都会以'Sync'结尾。要读取一个文件，你将需要使用`fs.readFileSync('/path/to/file')`方法。这个方法会返回一个包含文件完整内容的`Buffer`对象。

`fs`模块的文档，可以使用浏览器打开如下[路径]((file:///usr/local/lib/node_modules/learnyounode/node_apidoc/fs.html))来访问：

`Buffer`对象是`Node`用来高效处理数据的方式，无论该数据是`ascii`还是二进制文件，或者其他的格式。`Buffer`可以很容易地通过调用`toString()`方法转换为字符串。如:`var str = buf.toString()`。

`Buffer`的文档可以通过浏览器访问如下[路径](file:///usr/local/lib/node_modules/learnyounode/node_apidoc/buffer.html)来查看:

如果你在想如何更简单地去计算行数，请回想一下，一个 JavaScript字符串，可以使用`.split()`分割成子字符串数组，而且，'\n'可以作为分隔符。注意，供测试的文件末尾的最后一行并没有进行换行，即没有'\n' 的存在，因此，使用这个方法的话，所得的数组的长度会比行数多一个。

---
参考答案:

```js
var fs = require('fs')

    var contents = fs.readFileSync(process.argv[2])
    var lines = contents.toString().split('\n').length - 1
    console.log(lines)

    // 只要把 'utf8' 作为 readFileSync 的第二个参数传入
    // 就可以不用 .toString() 来得到一个字符串
    //
    // fs.readFileSync(process.argv[2], 'utf8').split('\n').length - 1
```

## 4. 第一个异步I/O

编写一个程序，执行一个异步的对文件系统的操作：读取一个文件，并且在终端（标准输出stdout）打印出这个文件中的内容的行数。类似于执行`cat file | wc -l`这个命令。

所要读取的文件的完整路径会在命令行第一个参数提供。

### 提示

这个题目的答案几乎和前一个问题一样，但是你需要用更加符合 Node.js的风格的方式解决：异步。

你将要使用`fs.readFile()`方法，而不是`fs.readFileSync()`，并且你需要从你所传入的回调函数中去收集数据（这些数据会作为第二参数传递给你的回调函数），而不是使用方法的返回值。想要学习更多关于
回调函数的知识，请查阅[文档](https://github.com/maxogden/art-of-node#callbacks)。

记住，我们习惯中的 Node.js 回调函数都有像如下所示的特征：

```js
function callback (err, data) { /* ... */ }
```

所以，你可以通过检查第一个参数的真假值来判断是否有错误发生。如果没有错误发生，你会在第二个参数中获取到一个`Buffer`对象。和`readFileSync()`一样，你可以传入 `'utf8'`作为它的第二个参数，然后把回调函数作为第三个参数，这样，你得到的将会是一个
字符串，而不是 `Buffer`。

`fs`模块的文档可以通过使用你的浏览器访问[路径](file:///usr/local/lib/node_modules/learnyounode/node_apidoc/fs.html)来查看。
