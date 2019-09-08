# JavaScript函数式编程

## 概念整理

- 柯里化
- 纯函数
- 偏函数

### 柯里化

[冴羽的博客-柯里化](https://github.com/mqyqingfeng/Blog/issues/42)

> 在数学和计算机科学中，柯里化是一种将使用多个参数的一个函数转换成一系列使用一个参数的函数的技术。

将一个多参数函数转换成多个单参数函数，也就是将一个 n 元函数转换成 n 个一元函数。

```js
const curry = fn =>
  judge = (...args) =>
    args.length === fn.length
      ? fn(...args)
      : (arg) => judge(...args, arg)
```

### 偏函数

[冴羽的博客-偏函数](https://github.com/mqyqingfeng/Blog/issues/43)

固定一个函数的一个或者多个参数，也就是将一个 n 元函数转换成一个 n - x 元函数。

```js
function partial(fn) {
  var args = [].slice.call(arguments, 1);
  return function() {
    var newArgs = args.concat([].slice.call(arguments));
    return fn.apply(this, newArgs);
  };
};
```

### 惰性函数

[冴羽的博客-惰性函数](https://github.com/mqyqingfeng/Blog/issues/44)

```js
var foo = function() {
  var t = new Date();
  foo = function() {
      return t;
  };
  return foo();
};
```

### 函数组合

[冴羽的博客-函数组合](https://github.com/mqyqingfeng/Blog/issues/45)

## JS 函数式编程指南

函数是不同数值之间的特殊关系：每一个输入值返回且只返回一个输出值。

在函数式编程中，函数就是一个管道，从头进去一个值，另一头出来一个新的值，没有其他作用。

纯函数必须遵守以下约束。

+ 不得改写参数
+ 不能调用系统I/O的API
+ 不能调用Date.now()或者Math.random()等不纯的方法

只要是跟函数外部环境发生的交互就都是副作用。

纯函数的好处

+ 总能够根据输入来做缓存(memoize)
+ 可移植性/self-document
+ 可测试性
+ 引用透明(如果一段代码可以替换成它执行所得的结果，而且是在不改变整个程序行为的前提下替换)
+ 并行代码，不会访问共享内存

函数的合成(compose)，一个值要经过多个函数，才能变成另外一个值，就可以把所有中间步骤合并成一个函数。

函数合成就像将管道连起来，让数据一口气从多个管道中穿过。

## curry

```js
const add = function(x) {
  return function(y) {
    return x + y
  }
}

const increment = add(1)
const addTen = add(10)

increment(2) // 3

addTen(2) // 12
```

定义了`add`函数，接受一个参数返回一个新的函数。调用`add`之后，返回的函数就通过闭包的方式记住了`add`的第一个参数。

通过传递一到两个参数调用函数，就能得到一个记住了这些参数的新函数。

## compose

```js
const compose = function(f, g) {
  return function(x) {
    return f(g(x))
  }
}
```

```js
// 摘自 https://github.com/reactjs/redux/blob/master/src/compose.js
export default function compose(...funcs) {
  if (funcs.length === 0) {
    return arg => arg
  }
  if (funcs.length === 1) {
    return funcs[0]
  }
  return funcs.reduce((a, b) => (...args) => a(b(...args)))
}
```

`f`和`g`都是函数，`x`是在他们之间通过"管道"传输的值。

函数的合成遵循结合律。

```js
compose(f, compose(g, h))
// 等同于
compose(compose(f, g), h)
// 等同于
compose(f, g, h)
```

### pointfree

无须提及将要操作的数据时什么样的

```js
// 非 pointfree，因为提到了数据：word
const snakeCase = function (word) {
  return word.toLowerCase().replace(/\s+/ig, '_');
};

// pointfree
const snakeCase = compose(replace(/\s+/ig, '_'), toLowerCase);
```

pointfree模式可以减少不必要的命名，让代码保持简洁和通用。

## Functional-Light-JS

arguments 实参、parameters 形参。

declarative 声明式、imperative 指令式

### 为什么使用函数式编程

> 我们在维护代码过程中 70％ 的时间花在了阅读和理解代码上。 也难怪全球程序员每天的平均代码行数是 5 行。我们一天花七个半小时用来读代码，然后找出这 5 行代码应该写在哪里。

函数式编程是编写可读代码的最有效工具之一。

### 函数基础

通过函数的 `length` 属性获取 Arity(函数声明的形参数量)。

```js
function foo(x,y,z) {
  // ..
}

foo.length;  // 3
```

### 管理函数的输入

偏函数(partially-applied functions)

```js
var partial =
  (fn, ...presetArgs) =>
    (...laterArgs) =>
      fn( ...presetArgs, ...laterArgs );

function add(x,y) {
  return x + y;
}

[1,2,3,4,5].map(function adder(val){
  return add(3, val);
});
// [4,5,6,7,8]

[1,2,3,4,5].map(partial( add, 3 ));
// [4,5,6,7,8]
```

使用偏函数来调整 `add` 函数签名，以符合 `map` 函数的预期。

```js
function partialRight( fn, ...presetArgs ) {
  return reverseArgs(
    partial( reverseArgs( fn ), ...presetArgs.reverse() )
  );
}
```

> 在 JavaScript 中，柯里化和偏应用都使用闭包来保存实参，直到收齐所有实参后我们再执行原函数。

严格柯里化，每次只传入一个实参，松散的柯里化(loose currying)，允许一次传入多个实参。

只要一个实参：

强制将一个函数处理成单参数函数(unary)

```js
var unary =
  fn =>
    arg =>
      fn( arg );

["1","2","3"].map(unary(parseInt));
// [1,2,3]
```

### 组合函数

```js
var compose =
  (...fns) => {
    // 拿出最后两个参数
    var [ fn1, fn2, ...rest ] = fns.reverse();

    var composedFn =
      (...args) =>
        fn2( fn1( ...args ) );

    if (rest.length == 0) return composedFn;

    return compose( ...rest.reverse(), composedFn );
  };
```

```js
function pipe(...fns) {
  return function piped(result){
    var list = fns.slice();

    while (list.length > 0) {
      // 从列表中取第一个函数并执行
      result = list.shift()( result );
    }

    return result;
  };
}
```

组合的实例

```js
// 提供该API：ajax( url, data, cb )
var getPerson = partial( ajax, "http://some.api/person" );
var getLastOrder = partial( ajax, "http://some.api/order", { id: -1 } );

getLastOrder( function orderFound(order){
  getPerson( { id: order.personId }, function personFound(person){
    output( person.name );
  });
});
```

```js
var prop =
  (name,obj) =>
    obj[name];

function setProp(name,obj,val) {
  var o = Object.assign( {}, obj );
  o[name] = val;
  return o;
}

var makeObjProp =
  (name,value) =>
    setProp( name, {}, value );
```

```js
var getPerson = partial( ajax, "http://some.api/person" );
var getLastOrder = partial( ajax, "http://some.api/order", { id: -1 } );

var extractName = partial( prop, "name" );
var outputPersonName = compose( output, extractName );
var processPerson = partialRight( getPerson, outputPersonName );
var personData = partial( makeObjProp, "id" );
var extractPersonId = partial( prop, "personId" );
var lookupPerson = compose( processPerson, personData, extractPersonId );

getLastOrder( lookupPerson );
```

### 减少副作用

我们需要做的不是完全消除副作用，而是让副作用尽可能的少，提高代码的可读性。

> 当我们在阅读程序的时候，能够清晰明确的识别每一个起因和每一个结果是非常重要的。在某种程度上，通读程序但不能看到因果的直接关系，程序的可读性就会降低。

幂等：从数学的角度来看，幂等指的是在第一次调用后，如果你将该输出一次又一次地输入到操作中，其输出永远不会改变的操作。换句话说，`foo(x)` 将产生与 `foo(foo(x))`、`foo(foo(foo(x)))` 等相同的输出。

给定相同的输入（一个或多个），它总是产生相同的输出。

引用透明性是指一个函数调用可以被它的输出值所代替，并且整个程序的行为不会改变。换句话说，不可能从程序的执行中分辨出函数调用是被执行的，还是它的返回值是在函数调用的位置上内联的。

```js
function calculateAverage(list) {
  var sum = 0;
  for (let i = 0; i < list.length; i++) {
    sum += list[i];
  }
  return sum / list.length;
}

var nums = [1,2,4,7,11,16,22];

var avg = calculateAverage( nums );
// 或者用注释的这句 ！！！
// var avg = 9;

console.log( "The average is:", avg ); // The average is: 9
```

在纯度这个问题上，我们要做到学术与实用主义相平衡。

如何将不纯的函数重构为纯函数？创建一个中间函数，用来隔离。这个方法比较笨拙，只适用于部分场景。

### 值的不可变性

值的不可变性：当需要改变程序中的状态时，我们不能改变已存在的数据，而是必须创建和跟踪一个新的数据。

常量：一个无法进行重新赋值(reassignment)的变量，无论常量承载何值，该变量都不能使用其他的值被进行重新赋值。但它与值的本质无关。

`const` 与值的本质无关，Java 引入了全新关键字 `final`

```js
const x = [ 2 ];

x[0] = 3;
```

通过类似 Immutable.js 这样的库来使用不可变数据是一个不错的方案。

### 闭包 vs 对象

- 一个没有闭包的编程语言可以用对象来模拟闭包。
- 一个没有对象的编程语言可以用闭包来模拟对象。

换句话说，我们可以认为闭包和对象是一样东西的两种表达方式。

看一个例子

```js
var person = {
  firstName: "Kyle",
  lastName: "Simpson",
  first() {
    return this.firstName;
  },
  last() {
    return this.lastName;
  }
}

person.first() + " " + person.last();
// Kyle Simpson
```

```js
function createPerson(firstName,lastName) {
  return API;

  function API(methodName) {
    switch (methodName) {
      case "first":
        return first();
        break;
      case "last":
        return last();
        break;
    };
  }

  function first() {
    return firstName;
  }

  function last() {
    return lastName;
  }
}

var person = createPerson( "Kyle", "Simpson" );

person( "first" ) + " " + person( "last" );
// Kyle Simpson
```

同构：两件事物A和B如果你能够映射（转化）A 到 B 并且能够通过反向映射回到A那么它们就是同构。

### 列表操作

- map 映射
- filter 过滤
- reduce 减少 将列表中多个值合并为一个值

reduce 有两种合并方式，有初始值和没有初始值

![有初始值](https://wizardforcel.gitbooks.io/functional-light-js/content/fig11.png)

![无初始值](https://wizardforcel.gitbooks.io/functional-light-js/content/fig12.png)

使用 `reduce` 来实现 `map`

```js
var double = v => v * 2;

[1,2,3,4,5].map( double );
// [2,4,6,8,10]

[1,2,3,4,5].reduce(
  (list,v) => (
    list.push( double( v ) ),
    list
  ), []
);
// [2,4,6,8,10]
```

```js
var isOdd = v => v % 2 == 1;

[1,2,3,4,5].filter( isOdd );
// [1,3,5]

[1,2,3,4,5].reduce(
  (list,v) => (
    isOdd( v ) ? list.push( v ) : undefined,
    list
  ), []
);
// [1,3,5]
```

两种风格写法

```js
[1,2,3,4,5]
.filter( isOdd )
.map( double )
.reduce( sum, 0 ); // 18

//  采用独立的方法.

reduce(
  map(
    filter( [1,2,3,4,5], isOdd ),
    double
  ),
  sum,
  0
); // 18
```

融合采用函数组合技术来合并多个相邻的 map(..)调用。这是常见的性能优化方式，并且它也使得列表操作更加自然。

```js
var removeInvalidChars = str => str.replace( /[^\w]*/g, "" );

var upper = str => str.toUpperCase();

var elide = str =>
  str.length > 10 ?
    str.substr( 0, 7 ) + "..." :
    str;

var words = "Mr. Jones isn't responsible for this disaster!"
  .split( /\s/ );

words;
// ["Mr.","Jones","isn't","responsible","for","this","disaster!"]

words
.map( removeInvalidChars )
.map( upper )
.map( elide );
// ["MR","JONES","ISNT","RESPONS...","FOR","THIS","DISASTER"]

// 代码融合之后
words
.map(
  compose( elide, upper, removeInvalidChars )
);
// ["MR","JONES","ISNT","RESPONS...","FOR","THIS","DISASTER"]
```

一个函数做一件事，不同的组合使用 `compose` 是一个不错的尝试
