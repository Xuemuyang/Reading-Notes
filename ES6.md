# [ECMAScript6 入门](http://es6.ruanyifeng.com/#README) ---阮一峰

## tips

1. `...`操作符

`...`用在函数参数中是把剩余参数转化为数组。

`...`用于数组操作则是将数组展开。

## 1. ECMAScript 6简介

> ECMAScript 6.0（以下简称 ES6）是`JavaScript`语言的下一代标准，已经在2015年6月正式发布了。它的目标，是使得`JavaScript`语言可以用来编写复杂的大型应用程序，成为企业级开发语言。

2011年，`ECMAScript5.1`版发布后，就开始制定6.0版了。因此，`ES6`这个词的原意，就是指`JavaScript`语言的下一个版本。

标准委员会最终决定，标准在每年的6月份正式发布一次，作为当年的正式版本。接下来的时间，就在这个版本的基础上做改动，直到下一年的6月份，草案就自然变成了新一年的版本。这样一来，就不需要以前的版本号了，只要用年份标记就可以了。

`ES6`的第一个版本，就这样在2015年6月发布了，正式名称就是《ECMAScript 2015标准》（简称`ES2015`）。2016年6月，小幅修订的《ECMAScript2016标准》（简称`ES2016`）如期发布，这个版本可以看作是`ES6.1`版，因为两者的差异非常小（只新增了数组实例的includes方法和指数运算符），基本上是同一个标准。根据计划，2017年6月发布`ES2017`标准。

因此，`ES6`既是一个历史名词，也是一个泛指，含义是5.1版以后的`JavaScript`的下一代标准，涵盖了`ES2015`、`ES2016`、`ES2017`等等，而`ES2015`则是正式名称，特指该年发布的正式版本的语言标准。本书中提到`ES6`的地方，一般是指`ES2015`标准，但有时也是泛指“下一代`JavaScript`语言”。

`Babel`配合`ESlint`使用进行ES6转码。

## 2. `let`和`const`

1. `let`

> ES6 新增了let命令，用来声明变量。它的用法类似于var，但是所声明的变量，只在let命令所在的代码块内有效。

```javascript
var a = [];
for (var i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6](); // 10
```

一个非常经典的ES5用闭包解决的例子，全局作用域中执行，函数活动对象中的`i`是全局对象中的`i`，这个`i`值为10，所以每一个输出都是10。

如果使用`let`，声明的变量仅在块级作用域内有效，最后输出的是6。

```javascript
var a = [];
for (let i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6](); // 6
```

`for`循环还有一个特别之处，就是设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域。

```javascript
for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}
// abc
// abc
// abc
```

上面代码正确运行，输出了3次`abc`。这表明函数内部的变量`i`与循环变量`i`不在同一个作用域，有各自单独的作用域。

### 不存在变量提升

`let`所声明的变量一定要在声明后使用，否则报错。

```javascript
// var 的情况
console.log(foo); // 输出undefined
var foo = 2;

// let 的情况
console.log(bar); // 报错ReferenceError
let bar = 2;
```

### 暂时性死区

只要块级作用域内存在`let`命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响。

```javascript
var tmp = 123;

if (true) {
  tmp = 'abc'; // ReferenceError
  let tmp;
}
```

上面代码中，存在全局变量`tmp`，但是块级作用域内`let`又声明了一个局部变量`tmp`，导致后者绑定这个块级作用域，所以在`let`声明变量前，对`tmp`赋值会报错。

ES6明确规定，如果区块中存在`let`和`const`命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。

总之，在代码块内，使用`let`命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）。

“暂时性死区”也意味着`typeof`不再是一个百分之百安全的操作。

```javascript
typeof x; // ReferenceError
let x;
```

ES6 规定暂时性死区和`let`、`const`语句不出现变量提升，主要是为了减少运行时错误，防止在变量声明前就使用这个变量，从而导致意料之外的行为。这样的错误在 ES5 是很常见的，现在有了这种规定，避免此类错误就很容易了。

总之，暂时性死区的本质就是，只要一进入当前作用域，所要使用的变量就已经存在了，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量。

### 不允许重复声明

```javascript
// 报错
function () {
  let a = 10;
  var a = 1;
}

// 报错
function () {
  let a = 10;
  let a = 1;
}
```

### 块级作用域

块级作用域的出现，实际上使得获得广泛应用的立即执行函数表达式（IIFE）不再必要了。

```javascript
// IIFE 写法
(function () {
  var tmp = ...;
  ...
}());

// 块级作用域写法
{
  let tmp = ...;
  ...
}
```

考虑到环境导致的行为差异太大，应该避免在块级作用域内声明函数。如果确实需要，也应该写成函数表达式，而不是函数声明语句。

```javascript
// 函数声明语句
{
  let a = 'secret';
  function f() {
    return a;
  }
}

// 函数表达式
{
  let a = 'secret';
  let f = function () {
    return a;
  };
}
```

**`do`表达式**

> 本质上，块级作用域是一个语句，将多个操作封装在一起，没有返回值。

现在有一个提案，使得块级作用域可以变为表达式，也就是说可以返回值，办法就是在块级作用域之前加上do，使它变为do表达式。

```javascript
let x = do {
  let t = f();
  t * t + 1;
};
```

上面代码中，变量x会得到整个块级作用域的返回值。

2.`const`命令

`const`声明一个只读的常量。一旦声明，常量的值就不能改变。

`const`声明的变量不得改变值，这意味着，`const`一旦声明变量，就必须立即初始化，不能留到以后赋值。

```javascript
const foo;
// SyntaxError: Missing initializer in const declaration
```

+ `const`的作用域与`let`命令相同：只在声明所在的块级作用域内有效。
+ `const`命令声明的常量也是不提升，同样存在暂时性死区，只能在声明的位置后面使用。
+ `const`声明的常量，也与`let`一样不可重复声明。

### 本质

`const`实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址不得改动。对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指针，`const`只能保证这个指针是固定的，至于它指向的数据结构是不是可变的，就完全不能控制了。因此，将一个对象声明为常量必须非常小心。

### ES6 声明变量的6中方式

> ES5 只有两种声明变量的方法：`var`命令和`function`命令。ES6除了添加`let`和`const`命令，后面章节还会提到，另外两种声明变量的方法：`import`命令和`class`命令。所以，ES6 一共有6种声明变量的方法。

3.顶层对象的属性

顶层对象，在浏览器环境指的是`window`对象，在`Node`指的是`global`对象。ES5之中，顶层对象的属性与全局变量是等价的。

```javascript
window.a = 1;
a // 1
```

```javascript
a = 2;
window.a // 2
```

上面代码中，顶层对象的属性赋值与全局变量的赋值，是同一件事。

顶层对象的属性与全局变量挂钩，被认为是JavaScript语言最大的设计败笔之一。这样的设计带来了几个很大的问题，首先是没法在编译时就报出变量未声明的错误，只有运行时才能知道（因为全局变量可能是顶层对象的属性创造的，而属性的创造是动态的）；其次，程序员很容易不知不觉地就创建了全局变量（比如打字出错）；最后，顶层对象的属性是到处可以读写的，这非常不利于模块化编程。另一方面，`window`对象有实体含义，指的是浏览器的窗口对象，顶层对象是一个有实体含义的对象，也是不合适的。

ES6为了改变这一点，一方面规定，为了保持兼容性，`var`命令和`function`命令声明的全局变量，依旧是顶层对象的属性；另一方面规定，`let`命令、`const`命令、`class`命令声明的全局变量，不属于顶层对象的属性。也就是说，从ES6开始，全局变量将逐步与顶层对象的属性脱钩。

```javascript
var a = 1;
// 如果在Node的REPL环境，可以写成global.a
// 或者采用通用方法，写成this.a
window.a // 1

let b = 1;
window.b // undefined
```

上面代码中，全局变量`a`由`var`命令声明，所以它是顶层对象的属性；全局变量`b`由`let`命令声明，所以它不是顶层对象的属性，返回`undefined`。

## 3. 变量的解构赋值

1. 数组的解构赋值

> ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构（Destructuring）。

`let [a, b, c] = [1, 2, 3];`

本质上，这种写法属于“模式匹配”，只要等号两边的模式相同，左边的变量就会被赋予对应的值。下面是一些使用嵌套数组进行解构的例子。

## 4. 字符串的扩展

传统上，JavaScript只有indexOf方法，可以用来确定一个字符串是否包含在另一个字符串中。ES6又提供了三种新方法。

+ `includes()`：返回布尔值，表示是否找到了参数字符串。
+ `startsWith()`：返回布尔值，表示参数字符串是否在原字符串的头部。
+ `endsWith()`：返回布尔值，表示参数字符串是否在原字符串的尾部。

```javascript
var s = 'Hello world!';

s.startsWith('Hello') // true
s.endsWith('!') // true
s.includes('o') // true
```

这三个方法都支持第二个参数，表示开始搜索的位置。

```javascript
var s = 'Hello world!';

s.startsWith('world', 6) // true
s.endsWith('Hello', 5) // true
s.includes('Hello', 6) // false
```

上面代码表示，使用第二个参数n时，`endsWith`的行为与其他两个方法有所不同。它针对前n个字符，而其他两个方法针对从第n个位置直到字符串结束。

`repeat()`方法

`repeat`方法返回一个新字符串，表示将原字符串重复n次。

```javascript
'x'.repeat(3) // "xxx"
'hello'.repeat(2) // "hellohello"
'na'.repeat(0) // ""
```

`padStart()`和`padEnd()`方法

如果某个字符串不够指定长度，会在头部或尾部补全。`padStart()`用于头部补全，`padEnd()`用于尾部补全。

```javascript
'x'.padStart(5, 'ab') // 'ababx'
'x'.padStart(4, 'ab') // 'abax'

'x'.padEnd(5, 'ab') // 'xabab'
'x'.padEnd(4, 'ab') // 'xaba'
```

### 模板字符串

模板字符串（template string）是增强版的字符串，用反引号（`）标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。

```javascript
// 普通字符串
`In JavaScript '\n' is a line-feed.`

// 多行字符串
`In JavaScript this is
 not legal.`

console.log(`string text line 1
string text line 2`);

// 字符串中嵌入变量
var name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`
```

如果使用模板字符串表示多行字符串，所有的空格和缩进都会被保留在输出之中。

```javascript
$('#list').html(`
<ul>
  <li>first</li>
  <li>second</li>
</ul>
`);
```

上面代码中，所有模板字符串的空格和换行，都是被保留的，比如`<ul>`标签前面会有一个换行。如果你不想要这个换行，可以使用trim方法消除它。

```javascript
$('#list').html(`
<ul>
  <li>first</li>
  <li>second</li>
</ul>
`.trim());
```

大括号内部可以放入任意的JavaScript表达式，可以进行运算，以及引用对象属性。

```javascript
var x = 1;
var y = 2;

`${x} + ${y} = ${x + y}`
// "1 + 2 = 3"

`${x} + ${y * 2} = ${x + y * 2}`
// "1 + 4 = 5"

var obj = {x: 1, y: 2};
`${obj.x + obj.y}`
// "3"
```

模板字符串之中还能调用函数。

```javascript
function fn() {
  return "Hello World";
}

`foo ${fn()} bar`
// foo Hello World bar
```

如果大括号中的值不是字符串，将按照一般的规则转为字符串。比如，大括号中是一个对象，将默认调用对象的toString方法。

## 7. 函数的扩展

ES6 允许为函数的参数设置默认值，即直接写在参数定义的后面。

```javascript
function log(x, y = 'World') {
  console.log(x, y);
}

log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', '') // Hello
```

### 与解构赋值结合使用

参数默认值可以与解构赋值的默认值，结合起来使用。

```javascript
function foo({x, y = 5}) {
  console.log(x, y);
}

foo({}) // undefined 5
foo({x: 1}) // 1 5
foo({x: 1, y: 2}) // 1 2
foo() // TypeError: Cannot read property 'x' of undefined
```

上面代码只使用了对象的解构赋值默认值，没有使用函数参数的默认值。只有当函数foo的参数是一个对象时，变量x和y才会通过解构赋值生成。如果函数foo调用时没提供参数，变量x和y就不会生成，从而报错。通过提供函数参数的默认值，就可以避免这种情况。

```javascript
function foo({x, y = 5} = {}) {
  console.log(x, y);
}

foo() // undefined 5
```

上面代码指定，如果没有提供参数，函数foo的参数默认为一个空对象。

**函数的`length`属性**

指定了默认值以后，函数的`length`属性，将返回没有指定默认值的参数个数。

```javascript
(function (a) {}).length // 1
(function (a = 5) {}).length // 0
(function (a, b, c = 5) {}).length // 2
```

### 作用域

一旦设置了参数的默认值，函数进行声明初始化时，参数会形成一个单独的作用域（context）。等到初始化结束，这个作用域就会消失。这种语法行为，在不设置参数默认值时，是不会出现的。

```javascript
var x = 1;

function f(x, y = x) {
  console.log(y);
}

f(2) // 2
```

上面代码中，参数y的默认值等于变量x。调用函数f时，参数形成一个单独的作用域。在这个作用域里面，默认值变量x指向第一个参数x，而不是全局变量x，所以输出是2。

```javascript
let x = 1;

function f(y = x) {
  let x = 2;
  console.log(y);
}

f() // 1
```

上面代码中，函数f调用时，参数y = x形成一个单独的作用域。这个作用域里面，变量x本身没有定义，所以指向外层的全局变量x。函数调用时，函数体内部的局部变量x影响不到默认值变量x。

**`rest`参数**

ES6 引入`rest`参数（形式为...变量名），用于获取函数的多余参数，这样就不需要使用`arguments`对象了。`rest`参数搭配的变量是一个数组，该变量将多余的参数放入数组中。

```javascript
function add(...values) {
  let sum = 0;

  for (var val of values) {
    sum += val;
  }

  return sum;
}

add(2, 5, 3) // 10
```

```javascript
function push(array, ...items) {
  items.forEach(function(item) {
    array.push(item);
    console.log(item);
  });
}

var a = [];
push(a, 1, 2, 3)
```

注意，rest 参数之后不能再有其他参数（即只能是最后一个参数），否则会报错。

```javascript
// 报错
function f(a, ...b, c) {
  // ...
}
```

### 箭头函数

ES6 允许使用“箭头”（=>）定义函数。

```javascript
var f = v => v;
```

上面的箭头函数等同于：

```javascript
var f = function(v) {
  return v;
};
```

如果箭头函数不需要参数或需要多个参数，就使用一个圆括号代表参数部分。

```javascript
var f = () => 5;
// 等同于
var f = function () { return 5 };

var sum = (num1, num2) => num1 + num2;
// 等同于
var sum = function(num1, num2) {
  return num1 + num2;
};
```

如果箭头函数的代码块部分多于一条语句，就要使用大括号将它们括起来，并且使用`return`语句返回。

箭头函数的一个用处是简化回调函数。

```javascript
// 正常函数写法
[1,2,3].map(function (x) {
  return x * x;
});

// 箭头函数写法
[1,2,3].map(x => x * x);
```

```javascript
// 正常函数写法
var result = values.sort(function (a, b) {
  return a - b;
});

// 箭头函数写法
var result = values.sort((a, b) => a - b);
```

箭头函数有几个使用注意点。

1. 函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。

1. 不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。

1. 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。

1. 不可以使用yield命令，因此箭头函数不能用作 Generator 函数。

第一点尤其值得注意。this对象的指向是可变的，但是在箭头函数中，它是固定的。

```javascript
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}

var id = 21;

foo.call({ id: 42 });
// id: 42
```

上面代码中，`setTimeout`的参数是一个箭头函数，这个箭头函数的定义生效是在`foo`函数生成时，而它的真正执行要等到100毫秒后。如果是普通函数，执行时`this`应该指向全局对象`window`，这时应该输出21。但是，箭头函数导致`this`总是指向函数定义生效时所在的对象（本例是{id: 42}），所以输出的是42。

> `this`指向的固定化，并不是因为箭头函数内部有绑定`this`的机制，实际原因是箭头函数根本没有自己的`this`，导致内部的`this`就是外层代码块的`this`。正是因为它没有`this`，所以也就不能用作构造函数。

箭头函数转成 ES5 的代码如下。

```javascript
// ES6
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}

// ES5
function foo() {
  var _this = this;

  setTimeout(function () {
    console.log('id:', _this.id);
  }, 100);
}
```

```javascript
function foo() {
  return () => {
    return () => {
      return () => {
        console.log('id:', this.id);
      };
    };
  };
}

var f = foo.call({id: 1});

var t1 = f.call({id: 2})()(); // id: 1
var t2 = f().call({id: 3})(); // id: 1
var t3 = f()().call({id: 4}); // id: 1
```

上面代码之中，只有一个`this`，就是函数`foo`的`this`，所以t1、t2、t3都输出同样的结果。因为所有的内层函数都是箭头函数，都没有自己的`this`，它们的`this`其实都是最外层`foo`函数的`this`。

### 尾调用优化

尾调用（Tail Call）是函数式编程的一个重要概念，本身非常简单，一句话就能说清楚，就是指某个函数的最后一步是调用另一个函数。

我们知道，函数调用会在内存形成一个“调用记录”，又称“调用帧”（call frame），保存调用位置和内部变量等信息。如果在函数`A`的内部调用函数`B`，那么在`A`的调用帧上方，还会形成一个B的调用帧。等到`B`运行结束，将结果返回到`A`，`B`的调用帧才会消失。如果函数B内部还调用函数`C`，那就还有一个`C`的调用帧，以此类推。所有的调用帧，就形成一个“调用栈”（call stack）。

注意，只有不再用到外层函数的内部变量，内层函数的调用帧才会取代外层函数的调用帧，否则就无法进行“尾调用优化”。

### 尾递归

函数调用自身，称为递归。如果尾调用自身，就称为尾递归。

递归非常耗费内存，因为需要同时保存成千上百个调用帧，很容易发生“栈溢出”错误（stack overflow）。但对于尾递归来说，由于只存在一个调用帧，所以永远不会发生“栈溢出”错误。

```js
function factorial(n) {
  if (n === 1) return 1;
  return n * factorial(n - 1);
}

factorial(5) // 120
```

上面代码是一个阶乘函数，计算n的阶乘，最多需要保存n个调用记录，复杂度 O(n) 。

如果改写成尾递归，只保留一个调用记录，复杂度 O(1) 。

```js
function factorial(n, total) {
  if (n === 1) return total;
  return factorial(n - 1, n * total);
}

factorial(5, 1) // 120
```

还有一个比较著名的例子，就是计算`Fibonacci`数列，也能充分说明尾递归优化的重要性。

非尾递归的`Fibonacci`数列实现如下。

```js
function Fibonacci (n) {
  if ( n <= 1 ) {return 1};

  return Fibonacci(n - 1) + Fibonacci(n - 2);
}

Fibonacci(10) // 89
Fibonacci(100) // 堆栈溢出
Fibonacci(500) // 堆栈溢出
```

尾递归优化过的`Fibonacci`数列实现如下。

```js
function Fibonacci2 (n , ac1 = 1 , ac2 = 1) {
  if( n <= 1 ) {return ac2};

  return Fibonacci2 (n - 1, ac2, ac1 + ac2);
}

Fibonacci2(100) // 573147844013817200000
Fibonacci2(1000) // 7.0330367711422765e+208
Fibonacci2(10000) // Infinity
```

由此可见，“尾调用优化”对递归操作意义重大，所以一些函数式编程语言将其写入了语言规格。ES6 是如此，第一次明确规定，所有 ECMAScript 的实现，都必须部署“尾调用优化”。这就是说，ES6 中只要使用尾递归，就不会发生栈溢出，相对节省内存。

**柯里化`curring`**

函数式编程有一个概念，叫做柯里化（currying），意思是将多参数的函数转换成单参数的形式。这里也可以使用柯里化。

```js
function currying(fn, n) {
  return function (m) {
    return fn.call(this, m, n);
  };
}

function tailFactorial(n, total) {
  if (n === 1) return total;
  return tailFactorial(n - 1, n * total);
}

const factorial = currying(tailFactorial, 1);

factorial(5) // 120
```

或者采用默认值

```js
function factorial(n, total = 1) {
  if (n === 1) return total;
  return factorial(n - 1, n * total);
}

factorial(5) // 120
```

## 8. 数组的扩展

### 扩展运算符(spread)

扩展运算符好比`rest`参数的逆运算，将一个数组转为用逗号分隔的参数序列。

```js
console.log(...[1, 2, 3])
// 1 2 3

console.log(1, ...[2, 3, 4], 5)
// 1 2 3 4 5

[...document.querySelectorAll('div')]
// [<div>, <div>, <div>]
```

扩展运算符与正常的函数参数可以结合使用，非常灵活。

```js
function f(v, w, x, y, z) { }
const args = [0, 1];
f(-1, ...args, 2, ...[3]);
```

扩展运算符后面还可以放置表达式。

```js
const arr = [
  ...(x > 0 ? ['a'] : []),
  'b',
];
```

如果扩展运算符后面是一个空数组，则不产生任何效果。

```js
[...[], 1]
// [1]
```

### 替代函数的 apply 方法

```js
// ES5 的写法
function f(x, y, z) {
  // ...
}
var args = [0, 1, 2];
f.apply(null, args);

// ES6的写法
function f(x, y, z) {
  // ...
}
let args = [0, 1, 2];
f(...args);
```

复制数组

```js
//ES5
const a1 = [1, 2];
const a2 = a1.concat();

a2[0] = 2;
a1 // [1, 2]

//ES6
const a1 = [1, 2];
// 写法一
const a2 = [...a1];
// 写法二
const [...a2] = a1; //解构
```

合并数组

```js
// ES5
[1, 2].concat(more)
// ES6
[1, 2, ...more]

var arr1 = ['a', 'b'];
var arr2 = ['c'];
var arr3 = ['d', 'e'];

// ES5的合并数组
arr1.concat(arr2, arr3);
// [ 'a', 'b', 'c', 'd', 'e' ]

// ES6的合并数组
[...arr1, ...arr2, ...arr3]
// [ 'a', 'b', 'c', 'd', 'e' ]
```

字符串操作

扩展运算符还可以将字符串转为真正的数组。

```js
[...'hello']
// [ "h", "e", "l", "l", "o" ]
```

上面的写法，有一个重要的好处，那就是能够正确识别四个字节的`Unicode`字符。

```js
'x\uD83D\uDE80y'.length // 4
[...'x\uD83D\uDE80y'].length // 3
```

上面代码的第一种写法，`JavaScript`会将四个字节的`Unicode`字符，识别为2个字符，采用扩展运算符就没有这个问题。因此，正确返回字符串长度的函数，可以像下面这样写。

```js
let str = 'x\uD83D\uDE80y';

str.split('').reverse().join('')
// 'y\uDE80\uD83Dx'

[...str].reverse().join('')
// 'y\uD83D\uDE80x'
```

### `Array.from()`

`Array.from()`方法将两类对象转为真正的数组：类似数组的对象(array-like Object)和可遍历的对象。

```js
// NodeList对象
let ps = document.querySelectorAll('p');
Array.from(ps).filter(p => {
  return p.textContent.length > 100;
});

// arguments对象
function foo() {
  var args = Array.from(arguments);
  // ...
}
```

### `Array.of()`

Array.of方法用于将一组值，转换为数组。

```js
Array.of(3, 11, 8) // [3,11,8]
Array.of(3) // [3]
Array.of(3).length // 1
```

### `copyWithin()`

数组实例的copyWithin方法，在当前数组内部，将指定位置的成员复制到其他位置（会覆盖原有成员），然后返回当前数组。也就是说，使用这个方法，会修改当前数组。

```js
Array.prototype.copyWithin(target, start = 0, end = this.length)
```

它接受三个参数。

+ target（必需）：从该位置开始替换数据。如果为负值，表示倒数。
+ start（可选）：从该位置开始读取数据，默认为0。如果为负值，表示倒数。
+ end（可选）：到该位置前停止读取数据，默认等于数组长度。如果为负值，表示倒数。

```js
// 将3号位复制到0号位
[1, 2, 3, 4, 5].copyWithin(0, 3, 4)
// [4, 2, 3, 4, 5]

// -2相当于3号位，-1相当于4号位
[1, 2, 3, 4, 5].copyWithin(0, -2, -1)
// [4, 2, 3, 4, 5]

// 将3号位复制到0号位
[].copyWithin.call({length: 5, 3: 1}, 0, 3)
// {0: 1, 3: 1, length: 5}

// 将2号位到数组结束，复制到0号位
let i32a = new Int32Array([1, 2, 3, 4, 5]);
i32a.copyWithin(0, 2);
// Int32Array [3, 4, 5, 4, 5]

// 对于没有部署 TypedArray 的 copyWithin 方法的平台
// 需要采用下面的写法
[].copyWithin.call(new Int32Array([1, 2, 3, 4, 5]), 0, 3, 4);
// Int32Array [4, 2, 3, 4, 5]
```

为什么这段代码的执行结果如下

```js
// 将3号位复制到0号位
[].copyWithin.call({length: 5, 3: 1}, 0, 3)
// {0: 1, 3: 1, length: 5}
```

> copytWithin方法并不要求this对象值是一个Array对象,类数组对象也是可以的，它会修改自己，然后返回，而不是返回一个copy值

`[].copyWithin`获取`copyWithin`函数对象
`call`为任何一个函数对象都有的方法
`call`方法的第1个参数为`call`方法运行的上下文，也就是我们经常遇到的函数调用时候的`this`。

`{length: 5, 3: 1}`这个对象具有一个`length`属性，那么其就是一个类数组对象（鸭子模式），并且这个对象具有一个属性`key`为3的值。这个对象等价于一个"数组对象",那么`copyWithin`方法在执行的时候读取类数组对象下标3到末尾的元素,赋值到指定位置。

```js
({0:undefined,1:undefined,2:undefined,3: 1,4:undefined,5:undefined,length: 5}).copyWithin(0,3,5);
//结果为：
{0:1,1:undefined,2:undefined,3: 1,4:undefined,5:undefined,length: 5};
//也就是
{0:1,3:1,length:5}
```

### 数组实例的`find()`和`findIndex()`

数组实例的`find`方法，用于找出第一个符合条件的数组成员。它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为`true`的成员，然后返回该成员。如果没有符合条件的成员，则返回`undefined`。

```js
[1, 4, -5, 10].find((n) => n < 0)
// -5
```

```js
[1, 5, 10, 15].find(function(value, index, arr) {
  return value > 9;
}) // 10
```

数组实例的`findIndex`方法的用法与`find`方法非常类似，返回第一个符合条件的数组成员的位置，如果所有成员都不符合条件，则返回`-1`。

```js
[1, 5, 10, 15].findIndex(function(value, index, arr) {
  return value > 9;
}) // 2
```

这两个方法都可以接受第二个参数，用来绑定回调函数的`this`对象。

```js
function f(v){
  return v > this.age;
}
let person = {name: 'John', age: 20};
[10, 12, 26, 15].find(f, person);    // 26
```

上面的代码中，`find`函数接收了第二个参数`person`对象，回调函数中的`this`对象指向`person`对象。

另外，这两个方法都可以发现`NaN`，弥补了数组的`indexOf`方法的不足。

```js
[NaN].indexOf(NaN)
// -1

[NaN].findIndex(y => Object.is(NaN, y))
// 0
```

上面代码中，`indexOf`方法无法识别数组的`NaN`成员，但是`findIndex`方法可以借助`Object.is`方法做到。

### 数组实例的`fill()`

`fill`方法使用给定值，填充一个数组。

```js
['a', 'b', 'c'].fill(7)
// [7, 7, 7]

new Array(3).fill(7)
// [7, 7, 7]
```

`fill`方法用于空数组的初始化非常方便。

`fill`方法还可以接受第二个和第三个参数，用于指定填充的起始位置和结束位置。

```js
['a', 'b', 'c'].fill(7, 1, 2)
// ['a', 7, 'c']
```

### 数组实例的`includes()`

`Array.prototype.includes`方法返回一个布尔值，表示某个数组是否包含给定的值，与字符串的`includes`方法类似。

```js
[1, 2, 3].includes(2)     // true
[1, 2, 3].includes(4)     // false
[1, 2, NaN].includes(NaN) // true
```

该方法的第二个参数表示搜索的起始位置，默认为`0`。如果第二个参数为负数，则表示倒数的位置，如果这时它大于数组长度（比如第二个参数为`-4`，但数组长度为`3`），则会重置为从`0`开始。

```js
[1, 2, 3].includes(3, 3);  // false
[1, 2, 3].includes(3, -1); // true
```

没有改方法之前我们使用数组的`indexOf`方法，检查是否包含某个值。

`indexOf`方法有两个缺点，一是不够语义化，它的含义是找到参数值的第一个出现位置，所以要去比较是否不等于`-1`，表达起来不够直观。二是，它内部使用严格相等运算符（`===`）进行判断，这会导致对`NaN`的误判。

## 对象的扩展

### 属性的简洁表示法

`ES6`允许在对象之中，直接写变量。这时，属性名为变量名, 属性值为变量的值。

```js
function f(x, y) {
  return {x, y};
}

// 等同于

function f(x, y) {
  return {x: x, y: y};
}

f(1, 2) // Object {x: 1, y: 2}
```

方法也可以简写

```js
const o = {
  method() {
    return "Hello!";
  }
};

// 等同于

const o = {
  method: function() {
    return "Hello!";
  }
};
```

### 属性名表达式

`ES6`允许字面量定义对象时，用表达式作为对象的属性名，即把表达式放在方括号内。

```js
let lastWord = 'last word';

const a = {
  'first word': 'hello',
  [lastWord]: 'world'
};

a['first word'] // "hello"
a[lastWord] // "world"
a['last word'] // "world"
```

注意，属性名表达式如果是一个对象，默认情况下会自动将对象转为字符串`[object Object]`，这一点要特别小心。

```js
const keyA = {a: 1};
const keyB = {b: 2};

const myObject = {
  [keyA]: 'valueA',
  [keyB]: 'valueB'
};

myObject // Object {[object Object]: "valueB"}
```

### `Object.is()`

比较两个值是否相等

不同之处只有两个:一是`+0`不等于`-0`，二是`NaN`等于自身。

```js
+0 === -0 //true
NaN === NaN // false

Object.is(+0, -0) // false
Object.is(NaN, NaN) // true
```

### `Object.assign()`

`Object.assign`方法用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target）。

`Object.assign`方法的第一个参数是目标对象，后面的参数都是源对象。

```js
const target = { a: 1, b: 1 };

const source1 = { b: 2, c: 2 };
const source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}
```

注意点:

1.浅拷贝

2.同名属性替换

3.数组的处理

将数组视为对象来处理，下标就是属性名。

```js
Object.assign([1, 2, 3], [4, 5])
// [4, 5, 3]
```

4.取值函数的处理

`Object.assign()`只能进行值的复制。

```js
const source = {
  get foo() { return 1 }
};
const target = {};

Object.assign(target, source)
// { foo: 1 }
```

有如下用途:

1.为对象添加属性

```js
class Point {
  constructor(x, y) {
    Object.assign(this, {x, y});
  }
}
```

2.为对象添加方法

```js
Object.assign(SomeClass.prototype, {
  someMethod(arg1, arg2) {
    ···
  },
  anotherMethod() {
    ···
  }
});

// 等同于下面的写法
SomeClass.prototype.someMethod = function (arg1, arg2) {
  ···
};
SomeClass.prototype.anotherMethod = function () {
  ···
};
```

3.克隆对象

```js
function clone(origin) {
  return Object.assign({}, origin);
}
```

上面代码将原始对象拷贝到一个空对象，就得到了原始对象的克隆。

不过，采用这种方法克隆，只能克隆原始对象自身的值，不能克隆它继承的值。如果想要保持继承链，可以采用下面的代码。

```js
function clone(origin) {
  let originProto = Object.getPrototypeOf(origin);
  return Object.assign(Object.create(originProto), origin);
}
```

4.合并多个对象

将多个对象合并到某个对象。

```js
const merge =
  (target, ...sources) => Object.assign(target, ...sources);
```

如果希望合并后返回一个新对象，可以改写上面函数，对一个空对象合并。

```js
const merge =
  (...sources) => Object.assign({}, ...sources);
```

5.为属性指定默认值

```js
const DEFAULTS = {
  logLevel: 0,
  outputFormat: 'html'
};

function processContent(options) {
  options = Object.assign({}, DEFAULTS, options);
  console.log(options);
  // ...
}
```

浅拷贝！！！

### `super`关键字

`this`关键字总是指向函数所在的当前对象，`ES6`新增了一个类似的关键字`super`，指向当前对象的原型对象。

### `Object.keys()`,`Object.values()`,`Object.entries()`

`ES5`引入了`Object.keys`方法，返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键名。

```js
var obj = { foo: 'bar', baz: 42 };
Object.keys(obj)
// ["foo", "baz"]
```

`ES2017`引入了跟`Object.keys`配套的`Object.values`和`Object.entries`，作为遍历一个对象的手段，供`for...of`循环使用。

```js
let {keys, values, entries} = Object;
let obj = { a: 1, b: 2, c: 3 };

for (let key of keys(obj)) {
  console.log(key); // 'a', 'b', 'c'
}

for (let value of values(obj)) {
  console.log(value); // 1, 2, 3
}

for (let [key, value] of entries(obj)) {
  console.log([key, value]); // ['a', 1], ['b', 2], ['c', 3]
}
```

### 对象的扩展运算符

对象的扩展运算符(`...`)用于取出参数对象的所有可遍历属性，拷贝到当前对象之中。

```js
let z = { a: 3, b: 4 };
let n = { ...z };
n // { a: 3, b: 4 }
```

用于合并两个对象。

```js
let ab = { ...a, ...b };
// 等同于
let ab = Object.assign({}, a, b);
```

## 10. `Symbol`

ES5中的对象属性名都是字符串，容易造成属性名的冲突，`Symbol`机制保证每个属性的名字都是独一无二，从根本上防止属性名的冲突。

ES6引入一种新的数据类型`Symbol`，表示独一无二的值。是`JavaScript`中的第七种数据类型。

`Symbol`值通过`Symbol`函数生成。这就是说，对象的属性名现在可以有两种类型，一种是原来就有的字符串，另一种就是新增的`Symbol`类型。凡是属性名属于`Symbol`类型，就都是独一无二的，可以保证不会与其他属性名产生冲突。

> `Symbol`函数前不能使用`new`命令，否则会报错。这是因为生成的`Symbol`是一个原始类型的值，不是对象。也就是说，由于`Symbol`值不是对象，所以不能添加属性。基本上，它是一种类似于字符串的数据类型。

`Symbol`函数可以接受一个字符串作为参数，表示对`Symbol`实例的描述，主要是为了在控制台显示，或者转为字符串时，比较容易区分。

```js
let s1 = Symbol('foo');
let s2 = Symbol('bar');

s1 // Symbol(foo)
s2 // Symbol(bar)

s1.toString() // "Symbol(foo)"
s2.toString() // "Symbol(bar)"
```

`Symbol`函数的参数只是表示对当前`Symbol`值的描述，因此相同参数的`Symbol`函数的返回值是不相等的。

```js
// 没有参数的情况
let s1 = Symbol();
let s2 = Symbol();

s1 === s2 // false

// 有参数的情况
let s1 = Symbol('foo');
let s2 = Symbol('foo');

s1 === s2 // false
```

`Symbol`值不能与其他类型的值进行运算，会报错。

### 作为属性名的`Symbol`

```js
let mySymbol = Symbol();

// 第一种写法
let a = {};
a[mySymbol] = 'Hello!';

// 第二种写法
let a = {
  [mySymbol]: 'Hello!'
};

// 第三种写法
let a = {};
Object.defineProperty(a, mySymbol, { value: 'Hello!' });

// 以上写法都得到同样结果
a[mySymbol] // "Hello!"
```

> `Symbol`值作为对象属性名时，不能用点运算符。

```js
const mySymbol = Symbol();
const a = {};

a.mySymbol = 'Hello!';
a[mySymbol] // undefined
a['mySymbol'] // "Hello!"
```

在对象的内部，使用`Symbol`值定义属性时，`Symbol`值必须放在方括号之中。

```js
let s = Symbol();

let obj = {
  [s]: function (arg) { ... }
};

obj[s](123);
```

### 属性名的遍历

`Symbol`作为属性名，该属性不会出现在`for...in`、`for...of`循环中，也不会被`Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()`返回。但是，它也不是私有属性，有一个`Object.getOwnPropertySymbols`方法，可以获取指定对象的所有`Symbol`属性名。

`Object.getOwnPropertySymbols`方法返回一个数组，成员是当前对象的所有用作属性名的`Symbol`值。

```js
const obj = {};
let a = Symbol('a');
let b = Symbol('b');

obj[a] = 'Hello';
obj[b] = 'World';

const objectSymbols = Object.getOwnPropertySymbols(obj);

objectSymbols
// [Symbol(a), Symbol(b)]
```

## 11. `Set`和`Map`数据结构

### `Set`

`ES6`提供了新的数据结构`Set`。它类似于数组，但是成员的值都是唯一的，没有重复的值。

```js
const s = new Set();

[2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));

for (let i of s) {
  console.log(i);
}
// 2 3 5 4
```

```js
// 去除数组的重复成员
[...new Set(array)]
```

### `WeakSet`

`WeakSet`与`Set`结构类似，是不重复的值的集合。与`Set`有两个区别。

`WeakSet`中的成员只能是对象，而不能是其他类型的值。其次`WeakSet`中的对象都是弱引用，即垃圾回收机制不考虑`WeakSet`对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于`WeakSet`之中。

### `Map`

`Map`数据结构类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当做键。

### `WeakMap`

两点区别

`WeakMap`只接受对象作为键名（`null`除外），不接受其他类型的值作为键名。其次，`WeakMap`的键名所指向的对象，不计入垃圾回收机制。

## 12. `Proxy`

`Proxy`可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。`Proxy`这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。

## 14. `Promise`对象

### 1. `Promise`的含义

> `Promise`是异步编程的一种解决方案，最早有社区提出，ES6原生提供`Promise`对象

两个特点

1.对象的状态不受外界影响，`Promise`对象代表一个异步操作，有三种状态：`pending`（进行中）、`fulfilled`（已成功）和`rejected`（已失败）。只有异步操作的结果可以决定当前是哪一种状态，这正是"Promise"的由来。

2.一旦状态改变就不会再变。`Promise`对象的状态改变只有两种可能，`Pending`到`fulfilled`，`Pending`到`rejected`。只要这两种情况发生，状态就凝固了。

`fullfilled`状态也可用`resolved`来指代。

有了`Promise`对象，就可以将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数。此外，`Promise`对象提供统一的接口，使得控制异步操作更加容易。

`Promise`也有一些缺点。首先，无法取消`Promise`，一旦新建它就会立即执行，无法中途取消。其次，如果不设置回调函数，`Promise`内部抛出的错误，不会反应到外部。第三，当处于`pending`状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

### 2. 基本用法

`Promise`对象是一个构造函数，用来生成`Promise`实例。

`Promise`构造函数接受一个函数作为参数，该函数的两个参数分别是`resolve`和`reject`。它们是两个函数，由`JavaScript`引擎提供，不用自己部署。

`resolve`函数的作用是，将`Promise`对象的状态从“未完成”变为“成功”（即从`pending`变为`resolved`），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；`reject`函数的作用是，将`Promise`对象的状态从“未完成”变为“失败”（即从`pending`变为`rejected`），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

`Promise`实例生成以后，可以用`then`方法分别指定`resolved`状态和`rejected`状态的回调函数。

`then`方法可以接受两个回调函数作为参数。第一个回调函数是`Promise`对象的状态变为`resolved`时调用，第二个回调函数是`Promise`对象的状态变为`rejected`时调用。其中，第二个函数是可选的，不一定要提供。这两个函数都接受`Promise`对象传出的值作为参数。

`Promise`新建后就会立即执行。

```js
let promise = new Promise(function(resolve, reject) {
  console.log('Promise');
  resolve('hehe');
});

promise.then(function(val) {
  console.log('resolved.'+val);
});

console.log('Hi!');

// Promise
// Hi!
// resolved.hehe
```

如果调用`resolve`函数和`reject`函数时带有参数，那么它们的参数会被传递给回调函数。`reject`函数的参数通常是`Error`对象的实例，表示抛出的错误；`resolve`函数的参数除了正常的值以外，还可能是另一个`Promise`实例，比如像下面这样。

```js
const p1 = new Promise(function (resolve, reject) {
  // ...
});

const p2 = new Promise(function (resolve, reject) {
  // ...
  resolve(p1);
})
```

上面代码中，`p1`和`p2`都是`Promise`的实例，但是`p2`的`resolve`方法将`p1`作为参数，即一个异步操作的结果是返回另一个异步操作。

注意，这时`p1`的状态就会传递给`p2`，也就是说，`p1`的状态决定了`p2`的状态。如果`p1`的状态是`pending`，那么`p2`的回调函数就会等待`p1`的状态改变；如果`p1`的状态已经是`resolved`或者`rejected`，那么`p2`的回调函数将会立刻执行。

```js
const p1 = new Promise(function (resolve, reject) {
  setTimeout(() => reject(new Error('fail')), 3000)
})

const p2 = new Promise(function (resolve, reject) {
  setTimeout(() => resolve(p1), 1000)
})

p2
  .then(result => console.log(result))
  .catch(error => console.log(error))
// Error: fail
```

上面代码中，`p1`是一个`Promise`，3秒之后变为`rejected`。`p2`的状态在1秒之后改变，`resolve`方法返回的是`p1`。由于`p2`返回的是另一个`Promise`，导致`p2`自己的状态无效了，由`p1`的状态决定`p2`的状态。所以，后面的`then`语句都变成针对后者（`p1`）。又过了2秒，`p1`变为`rejected`，导致触发`catch`方法指定的回调函数。

> 注意，调用`resolve`或`reject`并不会终结 `Promise`的参数函数的执行。

```js
new Promise((resolve, reject) => {
  resolve(1);
  console.log(2);
}).then(r => {
  console.log(r);
});
// 2
// 1
```

> `resolved`的`Promise`是在本轮事件循环的末尾执行，总是晚于本轮循环的同步任务。

一般来说，调用`resolve`或`reject`以后，`Promise`的使命就完成了，后继操作应该放到`then`方法里面，而不应该直接写在`resolve`或`reject`的后面。所以，最好在它们前面加上`return`语句，这样就不会有意外。

```js
new Promise((resolve, reject) => {
  return resolve(1);
  // 后面的语句不会执行
  console.log(2);
})
```

### 3. `Promise.prototype.then()`

`Promise`实例具有`then`方法，它的作用是为`Promise`实例添加状态改变时的回调函数。

`then`方法返回的是一个新`Promise`实例，因此可以采用链式写法，即`then`方法后面再调用另一个`then`方法。

采用链式的`then`，可以指定一组按照次序调用的回调函数。这时，前一个回调函数，有可能返回的还是一个`Promise`对象（即有异步操作），这时后一个回调函数，就会等待该`Promise`对象的状态发生变化，才会被调用。

```js
getJSON("/post/1.json").then(function(post) {
  return getJSON(post.commentURL);
}).then(function funcA(comments) {
  console.log("resolved: ", comments);
}, function funcB(err){
  console.log("rejected: ", err);
});
```

采用箭头函数，上面代码更简洁

```js
getJSON("/post/1.json").then(
  post => getJSON(post.commentURL)
).then(
  comments => console.log("resolved: ", comments),
  err => console.log("rejected: ", err)
);
```

## 15. Iterator 和 for...of 循环

需要一种统一的接口机制，来处理所有不同的数据结构。

遍历器（`Iterator`）就是这样一种机制。它是一种接口，为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署`Iterator`接口，就可以完成遍历操作（即依次处理该数据结构的所有成员）。

`Iterator`的作用有三个：一是为各种数据结构，提供一个统一的、简便的访问接口；二是使得数据结构的成员能够按某种次序排列；三是`ES6`创造了一种新的遍历命令`for...of`循环，`Iterator`接口主要供`for...of`消费。

`Iterator`的遍历过程是这样的。

（1）创建一个指针对象，指向当前数据结构的起始位置。也就是说，遍历器对象本质上，就是一个指针对象。

（2）第一次调用指针对象的`next`方法，可以将指针指向数据结构的第一个成员。

（3）第二次调用指针对象的`next`方法，指针就指向数据结构的第二个成员。

（4）不断调用指针对象的`next`方法，直到它指向数据结构的结束位置。

每一次调用`next`方法，都会返回数据结构的当前成员的信息。具体来说，就是返回一个包含`value`和`done`两个属性的对象。其中，`value`属性是当前成员的值，`done`属性是一个布尔值，表示遍历是否结束。

当使用`for...of`循环遍历某种数据结构时，该循环会自动去寻找`Iterator`接口。

原生具备`Iterator`接口的数据结构如下:

+ `Array`
+ `Map`
+ `Set`
+ `String`
+ `TypedArray`
+ 函数的`arguments`对象
+ `NodeList`对象

```js
let arr = ['a', 'b', 'c'];
let iter = arr[Symbol.iterator]();

iter.next() // { value: 'a', done: false }
iter.next() // { value: 'b', done: false }
iter.next() // { value: 'c', done: false }
iter.next() // { value: undefined, done: true }
```

### `for...of`循环

数组原生具备`iterator`接口。

```js
const arr = ['red', 'green', 'blue'];

for(let v of arr) {
  console.log(v); // red green blue
}

const obj = {};
obj[Symbol.iterator] = arr[Symbol.iterator].bind(arr);

for(let v of obj) {
  console.log(v); // red green blue
}
```

## 16. Generator 函数的语法

### 基本概念

`Generator`函数是`ES6`提供的一种异步编程解决方案，语法行为与传统函数完全不同。

`Generator`函数是一个普通函数，但是有两个特征。一是，`function`关键字与函数名之间有一个星号；二是，函数体内部使用`yield`表达式，定义不同的内部状态（`yield`在英语里的意思就是“产出”）。

```js
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}

var hw = helloWorldGenerator();
```

上面代码定义了一个`Generator`函数`helloWorldGenerator`，它内部有两个`yield`表达式（`hello`和`world`），即该函数有三个状态：`hello`，`world`和`return`语句（结束执行）。

调用`Generator`函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，也就是上一章介绍的遍历器对象（`Iterator Object`）。

```js
hw.next()
// { value: 'hello', done: false }

hw.next()
// { value: 'world', done: false }

hw.next()
// { value: 'ending', done: true }

hw.next()
// { value: undefined, done: true }
```

第一次调用，`Generator`函数开始执行，直到遇到第一个`yield`表达式为止。`next`方法返回一个对象，它的`value`属性就是当前`yield`表达式的值`hello`，`done`属性的值`false`，表示遍历还没有结束。

第二次调用，`Generator`函数从上次`yield`表达式停下的地方，一直执行到下一个`yield`表达式。`next`方法返回的对象的`value`属性就是当前`yield`表达式的值`world`，`done`属性的值`false`，表示遍历还没有结束。

第三次调用，`Generator`函数从上次`yield`表达式停下的地方，一直执行到`return`语句（如果没有`return`语句，就执行到函数结束）。`next`方法返回的对象的`value`属性，就是紧跟在`return`语句后面的表达式的值（如果没有`return`语句，则`value`属性的值为`undefined`），`done`属性的值`true`，表示遍历已经结束。

第四次调用，此时`Generator`函数已经运行完毕，`next`方法返回对象的`value`属性为`undefined`，`done`属性为`true`。以后再调用`next`方法，返回的都是这个值。

总结一下，调用`Generator`函数，返回一个遍历器对象，代表`Generator`函数的内部指针。以后，每次调用遍历器对象的`next`方法，就会返回一个有着`value`和`done`两个属性的对象。`value`属性表示当前的内部状态的值，是`yield`表达式后面那个表达式的值；`done`属性是一个布尔值，表示是否遍历结束。

`ES6`没有规定，`function`关键字与函数名之间的星号，写在哪个位置。这导致下面的写法都能通过。

```js
function * foo(x, y) { ··· }
function *foo(x, y) { ··· }
function* foo(x, y) { ··· }
function*foo(x, y) { ··· }
```

### `yield`表达式

由于`Generator`函数返回的遍历器对象，只有调用`next`方法才会遍历下一个内部状态，所以其实提供了一种可以暂停执行的函数。`yield`表达式就是暂停标志。

遍历器对象的`next`方法的运行逻辑如下。

（1）遇到`yield`表达式，就暂停执行后面的操作，并将紧跟在`yield`后面的那个表达式的值，作为返回的对象的`value`属性值。

（2）下一次调用`next`方法时，再继续往下执行，直到遇到下一个`yield`表达式。

（3）如果没有再遇到新的`yield`表达式，就一直运行到函数结束，直到`return`语句为止，并将`return`语句后面的表达式的值，作为返回的对象的`value`属性值。

（4）如果该函数没有`return`语句，则返回的对象的`value`属性值为`undefined`。

需要注意的是，`yield`表达式后面的表达式，只有当调用`next`方法、内部指针指向该语句时才会执行，因此等于为`JavaScript`提供了手动的“惰性求值”（`Lazy Evaluation`）的语法功能。

> `yield`表达式只能用在`Generator`函数中，其他地方都会报错。

### `next`方法的参数

`yield`表达式本身没有返回值，或者说总是返回`undefined`。`next`方法可以带一个参数，该参数就会被当作上一个`yield`表达式的返回值。

```js
function* foo(x) {
  var y = 2 * (yield (x + 1));
  var z = yield (y / 3);
  return (x + y + z);
}

var a = foo(5);
a.next() // Object{value:6, done:false}
a.next() // Object{value:NaN, done:false}
a.next() // Object{value:NaN, done:true}

var b = foo(5);
b.next() // { value:6, done:false }
b.next(12) // { value:8, done:false }
b.next(13) // { value:42, done:true }
```

上面代码中，第二次运行`next`方法的时候不带参数，导致`y`的值等于`2 * undefined`（即`NaN`），除以`3`以后还是`NaN`，因此返回对象的`value`属性也等于`NaN`。第三次运行`Next`方法的时候不带参数，所以`z`等于`undefined`，返回对象的`value`属性等于`5 + NaN + undefined`，即`NaN`。

如果向`next`方法提供参数，返回结果就完全不一样了。上面代码第一次调用`b`的`next`方法时，返回`x+1`的值`6`；第二次调用`next`方法，将上一次`yield`表达式的值设为`12`，因此`y`等于`24`，返回`y / 3`的值`8`；第三次调用`next`方法，将上一次`yield`表达式的值设为`13`，因此`z`等于`13`，这时`x`等于`5`，`y`等于`24`，所以`return`语句的值等于`42`。

### `for...of`循环

`for...of`循环可以自动遍历`Generator`函数时生成的`Iterator`对象，且此时不再需要调用`next`方法。

```js
function* foo() {
  yield 1;
  yield 2;
  yield 3;
  yield 4;
  yield 5;
  return 6;
}

for (let v of foo()) {
  console.log(v);
}
// 1 2 3 4 5
```

### `Generator.prototype.throw()`

`Generator`函数返回的遍历器对象，都有一个`throw`方法，可以在函数体外抛出错误，然后在`Generator`函数体内捕获。

```js
var g = function* () {
  try {
    yield;
  } catch (e) {
    console.log('内部捕获', e);
  }
};

var i = g();
i.next();

try {
  i.throw('a');
  i.throw('b');
} catch (e) {
  console.log('外部捕获', e);
}
// 内部捕获 a
// 外部捕获 b
```

### `Generator.prototypr.return()`

`Generator`函数返回的遍历器对象，还有一个`return`方法，可以返回给定的值，并且终结遍历`Generator`函数。

```js
function* gen() {
  yield 1;
  yield 2;
  yield 3;
}

var g = gen();

g.next()        // { value: 1, done: false }
g.return('foo') // { value: "foo", done: true }
g.next()        // { value: undefined, done: true }
```

> `next()`、`throw()`、`return()`这三个方法本质上是同一件事，可以放在一起理解。它们的作用都是让 `Generator`函数恢复执行，并且使用不同的语句替换`yield`表达式。

### 应用

异步操作的同步化表达

通过`Generator`函数部署`Ajax`操作

```js
function* main() {
  var result = yield request("http://some.url");
  var resp = JSON.parse(result);
    console.log(resp.value);
}

function request(url) {
  makeAjaxCall(url, function(response){
    it.next(response);
  });
}

var it = main();
it.next();
```

## 17. `Generator`函数的异步应用

`ES6`诞生以前，异步编程的方法，大概有下面四种。

+ 回调函数
+ 事件监听
+ 发布/订阅
+ `Promise`对象

`Generator`函数将`JavaScript`异步编程带入了一个全新的阶段。

所谓异步，简单来说就是一个任务不是连续完成的，被分为两段，先执行第一段，然后转而执行其他任务，等做好准备，再回过头执行第二段。

### 回调函数

所谓回调函数，就是把任务的第二段单独写在一个函数里面，等到重新执行这个任务的时候，就直接调用这个函数。

```js
fs.readFile('/etc/passwd', 'utf-8', function (err, data) {
  if (err) throw err;
  console.log(data);
});
```

一个有趣的问题是，为什么`Node`约定，回调函数的第一个参数，必须是错误对象`err`（如果没有错误，该参数就是`null`）？

原因是执行分成两段，第一段执行完以后，任务所在的上下文环境就已经结束了。在这以后抛出的错误，原来的上下文环境已经无法捕捉，只能当作参数，传入第二段。

### `Promise`

回调函数容易形成`callback hell`。

`Promise`对象将回调函数的嵌套改成链式调用。

### `Generator`函数

#### 协程

传统的编程语言，早有异步编程的解决方案（其实是多任务的解决方案）。其中有一种叫做"协程"（`coroutine`），意思是多个线程互相协作，完成异步任务。

协程有点像函数，又有点像线程。它的运行流程大致如下。

第一步，协程`A`开始执行。
第二步，协程`A`执行到一半，进入暂停，执行权转移到协程`B`。
第三步，（一段时间后）协程`B`交还执行权。
第四步，协程`A`恢复执行。
上面流程的协程`A`，就是异步任务，因为它分成两段（或多段）执行。

举例来说，读取文件的协程写法如下。

```js
function* asyncJob() {
  // ...其他代码
  var f = yield readFile(fileA);
  // ...其他代码
}
```

上面代码的函数`asyncJob`是一个协程，它的奥妙就在其中的`yield`命令。它表示执行到此处，执行权将交给其他协程。也就是说，`yield`命令是异步两个阶段的分界线。

协程遇到`yield`命令就暂停，等到执行权返回，再从暂停的地方继续往后执行。它的最大优点，就是代码的写法非常像同步操作，如果去除`yield`命令，简直一模一样。

#### 协程的`Generator`函数实现

`Generator`函数是协程在`ES6`的实现，最大特点就是可以交出函数的执行权（即暂停执行）。

整个`Generator`函数就是一个封装的异步任务，或者说是异步任务的容器。异步操作需要暂停的地方，都用`yield`语句注明。`Generator`函数的执行方法如下。

```js
function* gen(x) {
  var y = yield x + 2;
  return y;
}

var g = gen(1);
g.next() // { value: 3, done: false }
g.next() // { value: undefined, done: true }
```

上面代码中，调用`Generator`函数，会返回一个内部指针（即遍历器）`g`。这是`Generator`函数不同于普通函数的另一个地方，即执行它不会返回结果，返回的是指针对象。调用指针`g`的`next`方法，会移动内部指针（即执行异步任务的第一段），指向第一个遇到的`yield`语句，上例是执行到`x + 2`为止。

换言之，`next`方法的作用是分阶段执行`Generator`函数。每次调用`next`方法，会返回一个对象，表示当前阶段的信息（`value`属性和`done`属性）。`value`属性是`yield`语句后面表达式的值，表示当前阶段的值；`done`属性是一个布尔值，表示`Generator`函数是否执行完毕，即是否还有下一个阶段。

#### `Generator`函数的数据交换和错误处理

`Generator`函数可以暂停执行和恢复执行，这是它能封装异步任务的根本原因。除此之外，还有函数体内外的数据交换和错误处理机制。

`next`返回值的`value`属性，是`Generator`函数向外输出数据；`next`方法还可以接受参数，向`Generator`函数体内输入数据。

```js
function* gen(x){
  var y = yield x + 2;
  return y;
}

var g = gen(1);
g.next() // { value: 3, done: false }
g.next(2) // { value: 2, done: true }
```

#### 异步任务的封装

```js
var fetch = require('node-fetch');

function* gen(){
  var url = 'https://api.github.com/users/github';
  var result = yield fetch(url);
  console.log(result.bio);
}
```

执行的方法如下

```js
var g = gen();
var result = g.next();

result.value.then(function(data){
  return data.json();
}).then(function(data){
  g.next(data);
});
```

## 18. `async`函数

`async`函数是`Generator`函数的语法糖。

```js
const gen = function* () {
  const f1 = yield readFile('/etc/fstab');
  const f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};

const asyncReadFile = async function () {
  const f1 = await readFile('/etc/fstab');
  const f2 = await readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```

一比较就会发现，`async`函数就是将`Generator`函数的`*`替换成`async`，将`yield`替换成`await`

`async`函数与`Promise`结合使用，可以将`Promise.then`中的链式写法改成语义更明了的“同步”写法。

### 基本用法

## Module

使用`export default`命令，为模块指定默认输出，这样就不需要知道所要加载模块的变量名，通过`export`方式导出，在导入时要加`{ }`，`export default`则不需要。

### 一个简单的作用域测试

export Module

```js
const a = 'export'

export const geta = () => {
  console.log(a)
}
```

import Module

```js
const a = 'import'

import geta from './export.js'

geta()
```

1. 当两个模块都定义`a`结果是`export`
2. export模块没有`a`，import模块有`a`则报错
3. export模块有`a`，import没有结果是`export`

### 严格模式

`ES6`的模块自动采用严格模式，不管你有没有在模块头部加上`use strict`。

严格模式主要有以下限制:

+ 变量必须声明后再使用
+ 函数的参数不能有同名属性，否则报错
+ 不能使用`with`语句
+ 不能对只读属性赋值，否则报错
+ 不能使用前缀 0 表示八进制数，否则报错
+ 不能删除不可删除的属性，否则报错
+ 不能删除变量`delete prop`，会报错，只能删除属性`delete global[prop]`
+ `eval`不会在它的外层作用域引入变量
+ `eval`和`arguments`不能被重新赋值
+ `arguments`不会自动反映函数参数的变化
+ 不能使用`arguments.callee`
+ 不能使用`arguments.caller`
+ 禁止`this`指向全局对象
+ 不能使用`fn.caller`和`fn.arguments`获取函数调用的堆栈
+ 增加了保留字（比如`protected`、`static`和`interface`）

### export命令

```js
// profile.js
export var firstName = 'Michael';
export var lastName = 'Jackson';
export var year = 1958;
```

或者采用

```js
// profile.js
var firstName = 'Michael';
var lastName = 'Jackson';
var year = 1958;

export {firstName, lastName, year};
```

优先考虑采用下面的写法，在脚本尾部，更清楚输出哪些变量。

`export`命令规定的是对外的接口，必须与模块内部的变量建立一一对应的关系。

```js
// 报错
export 1;

// 报错
var m = 1;
export m;
```

需要在接口名与模块内部的变量之间建立一一对应的关系。

```js
// 写法一
export var m = 1;

// 写法二
var m = 1;
export {m};

// 写法三
var n = 1;
export {n as m};
```

### import命令

```js
// main.js
import {firstName, lastName, year} from './profile.js';

function setName(element) {
  element.textContent = firstName + ' ' + lastName;
}
```

使用`as`关键字命名

```js
import { lastName as surname } from './profile.js';
```

`import`命令具有提升效果，会提升到整个模块的头部，首先执行。

`import`是静态执行，不能使用表达式和变量。

### export default命令

加载`export default`命令的模块，`import`命令为其制定任意名字。

### 浏览器加载

浏览器加载 ES6 模块，也使用`<script>`标签，但是要加入`type="module"`属性。

浏览器对于带有`type="module"`的`<script>`，都是异步加载，不会造成堵塞浏览器，即等到整个页面渲染完，再执行模块脚本，等同于打开了`<script>`标签的`defer`属性。