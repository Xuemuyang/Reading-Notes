# I don't know JS

# 作用域和闭包

## chap1. 作用域

### 1.1 编译原理

> 尽管通常将 JavaScript 归类为“动态”或“解释执行”语言，但事实上它是一门编译语言。

传统编译语言代码执行之前经历三个步骤
1. 分词/词法分析(Tokenizing/Lexing)
这个过程将字符组成的字符串分解成有意义的代码块(token)，比如`var a = 2;`通常被分解为`var`、`a`、`=`、`2`、`;`
2. 解析/语法分析(Parsing)
将词法单元流转化弄成一个结构树。抽象语法树(Abstract Syntax Tree，AST)
3. 代码生成
将AST转化为一组机器指令

### 1.2 理解作用域

+ 引擎
从头到尾负责整个`JavaScript`程序的编译及执行过程。
+ 编译器 
引擎的好朋友之一，负责语法分析及代码生成等脏活累活。
+ 作用域 
引擎的另一位好朋友，负责收集并维护由所有声明的标识符(变量)组成的一系列查询，并实施一套非常严格的规则，确定当前执行的代码对这些标识符的访问权限。

以`var a = 2;`为例，说明编译器、引擎和作用域如何协同工作。

编译器会进行如下处理

1. 遇到`var a`，编译器会询问作用域是否已经有一个该名称的变量存在于同一个作用域的集合中。如果是，编译器会忽略该声明，继续进行编译;否则它会要求作用域在当前作用域的集合中声明一个新的变量，并命名为`a`。

2. 接下来编译器会为引擎生成运行时所需的代码，这些代码被用来处理`a = 2`这个赋值操作。引擎运行时会首先询问作用域，在当前的作用域集合中是否存在一个叫作`a`的变量。如果是，引擎就会使用这个变量;如果否，引擎会继续查找该变量。

> 总结:变量的赋值操作会执行两个动作，首先编译器会在当前作用域中声明一个变量(如果之前没有声明过)，然后在运行时引擎会在作用域中查找该变量，如果能够找到就会对它赋值。

#### LHS和RHS查询

+ LHS-Left Hand Side
+ RHS-Right Hand Side

当变量出现在赋值操作的左侧时进行`LHS`查询，出现在右侧时进行`RHS`查询。`RHS`查询与简单地查找某个变量的值别无二致，而`LHS`查询则是试图找到变量的容器本身，从而可以对其赋值。

`console.log( a );`这里对`a`的引用是`LHS`引用。

下面的程序既有`LHS`引用也有`RHS`引用
```javascript
function foo(a) { console.log( a ); // 2
}
foo( 2 );
```

> 代码中隐式的`a=2`操作可能很容易被你忽略掉。这个操作发生在`2`被当作参数传递给`foo(..)`函数时，`2` 会被分配给参数`a`。为了给参数`a`(隐式地)分配值，需要进行一次`LHS`查询。

```javascript
function foo(a) {
    var b = a;
    return a + b; 
}

var c = foo(2);
```

三处`LHS`查询,四处`RHS`查询
`foo(2)`RHS
`a=2`LHS
`a`RHS
`b`LHS
`a`RHS
`b`RHS
`c`LHS

### 1.3 作用域嵌套

> 当一个块或函数嵌套在另一个块或函数中时，就发生了作用域的嵌套。因此，在当前作用域中无法找到某个变量时，引擎就会在外层嵌套的作用域中继续查找，直到找到该变量，或抵达最外层的作用域(也就是全局作用域)为止。

```javascript
function foo(a) { 
    console.log(a + b);
    b = a;
}
foo( 2 );
```

第一次对`b`进行`RHS`查询时是无法找到该变量的。也就是说，这是一个“未声明”的变量，因为在任何相关的作用域中都无法找到它。

如果`RHS`查询在所有嵌套的作用域中遍寻不到所需的变量，引擎就会抛出`ReferenceError`异常。值得注意的是，`ReferenceError`是非常重要的异常类型。

如果`RHS`查询找到了一个变量，但是你尝试对这个变量的值进行不合理的操作，比如试图对一个非函数类型的值进行函数调用，或着引用`null`或`undefined`类型的值中的属性，那么引擎会抛出另外一种类型的异常，叫作`TypeError`。

> `ReferenceError`同作用域判别失败相关，而`TypeError`则代表作用域判别成功了，但是对结果的操作是非法或不合理的。

### 总结一下
+ 变量（标识符）
+ 如果查找的目的是对变量进行赋值，那么就会使用`LHS`查询;如果目的是获取变量的值，就会使用`RHS`查询。

## chap 2.词法作用域

### 2.1 词法阶段

> 无论函数在哪里被调用，也无论它如何被调用，它的词法作用域都只由函数被声明时所处的位置决定。

### 2.2 欺骗词法

`JavaScript`中的`eval(..)`函数可以接受一个字符串为参数，并将其中的内容视为好像在书写时就存在于程序中这个位置的代码。换句话说，可以在你写的代码中用程序生成代码并运行，就好像代码是写在那个位置的一样。

#### `eval`

```javascript
function foo(str, a) { 
    eval( str ); // 欺骗! 
    console.log( a, b );
}
var b = 2;
foo( "var b = 3;", 1 ); // 1, 3
```

#### `with`

```javascript
var obj = { 
    a: 1,
    b: 2,
    c: 3 
};
// 单调乏味的重复 "obj" 
obj.a = 2;
obj.b = 3;
obj.c = 4;
// 简单的快捷方式 
with (obj) {
    a = 3;
    b = 4;
    c = 5;
}
```

#### 性能

会导致代码运行变慢，不要使用它们。

## chap3. 函数作用域和块作用域

### IIFE(Immediately Invoked Function Expression)

## chap4. 提升

VO按照如下顺序填充：
1. 函数参数
2. 函数声明
3. 变量声明

> 我们习惯将`var a = 2;`看作一个声明，而实际上`JavaScript`引擎并不这么认为。它将`var a`
和`a = 2`当作两个单独的声明，第一个是编译阶段的任务，而第二个则是执行阶段的任务。

## chap5. 作用域闭包

> 本质上无论何时何地，如果将函数(访问它们各自的词法作用域)当作第一级的值类型并到处传递，你就会看到闭包在这些函数中的应用。在定时器、事件监听器、 Ajax请求、跨窗口通信、Web Workers或者任何其他的异步(或者同步)任务中，只要使用了回调函数，实际上就是在使用闭包!

```javascript
for (var i=1; i<=5; i++) { 
    (function(j) {setTimeout(function timer() { 
        console.log( j );
        }, j*1000 );
    })(i);
}
```
在这个经典的闭包例子中，使用IIFE的目的是为了创建一个"块级"作用域，用来保存i，如果不使用IIFE，那么需要一个函数作用域来保存。

## 附录A 动态作用域

```javascript
function foo() { 
    console.log( a ); // 2
}
function bar() { 
    var a = 3;
    foo(); 
}
var a = 2; 
bar();
```
词法作用域让`foo()`中的`a`通过`RHS`引用到了全局作用域中的`a`，因此会输出2。

动态作用域并不关心函数和作用域是如何声明以及在何处声明的，只关心它们从何处调用。换句话说，作用域链是基于调用栈的，而不是代码中的作用域嵌套。

> 主要区别:词法作用域是在写代码或者说定义时确定的，而动态作用域是在运行时确定的。(`this`也是!)词法作用域关注函数在何处声明，而动态作用域关注函数从何处调用。

需要明确的是，事实上`JavaScript`并不具有动态作用域。它只有词法作用域，简单明了。但是`this`机制某种程度上很像动态作用域。

## 附录C `this`词法

# `this`和对象原型

## chap1. 关于`this`

`this`实际上是在函数被调用时发生的绑定，它的指向完全取决于函数在哪里被调用。

## chap2. `this`全面解析

### 2.1 调用位置

> 最重要的就是分析调用栈，我们关心的调用位置就在当前正在执行的函数的前一个调用中。

```javascript
function baz() {
    // 当前调用栈是:baz
    // 因此，当前调用位置是全局作用域
    console.log( "baz" );
    bar(); // <-- bar的调用位置 
}
function bar() {
    // 当前调用栈是 baz -> bar
    // 因此，当前调用位置在 baz 中
    console.log( "bar" );
    foo(); // <-- foo的调用位置 
}
function foo() {
    // 当前调用栈是 baz -> bar -> foo 
    // 因此，当前调用位置在 bar 中
    console.log( "foo" );
}
baz(); // <-- baz的调用位置
```

### 2.2 绑定规则

1. 默认绑定

独立函数调用 --> 默认绑定

```javascript
function foo() {
    console.log(this.a);
}
var a = 2;
foo(); //2
```

`foo()`是直接使用不带任何修饰的函数引用进行调用的，因此只能使用默认绑定，无法应用其他规则。

2. 隐式绑定

```javascript
function foo() {
    console.log(this.a);
}

var obj = {
    a: 2,
    foo: foo
};

obj.foo(); //2
```

> 无论是直接在`obj`中定义还是先定义再添加为引用属性，这个函数严格来说都不属于`obj`对象。

对象属性引用链中只有最顶层或者说最后一层会影响调用位置。

```javascript
function foo() {
    console.log(this.a);
}

var obj2 = {
    a: 42,
    foo: foo
};

var obj1 = {
    a: 2,
    obj2: obj2
};

obj1.obj2.foo(); //42
```

**隐式丢失**

```javascript
function foo() {
    console.log(this.a);
}

var obj = {
    a: 2,
    foo: foo
};

var bar = obj.foo; //函数别名

var a = "oops, global"; //a是全局对象的属性

bar(); //"oops, global"
```

> 虽然`bar`是`obj.foo`的一个引用，但是实际上，它引用的是`foo`函数本身，因此此时的`bar()`是一个不带任何修饰的函数调用，因此应用了默认绑定。

```javascript
function foo() {
    console.log(this.a);
}

function doFoo(fn) {
    // fn其实引用的是foo
    fn(); //<--调用位置
}

var obj = {
    a: 2,
    foo: foo
};

var a = "oops, global"; //a是全局对象的属性

doFoo(obj.foo); //"oops, global"
```

回调函数会丢失`this`绑定

3. 显式绑定

+ `call`
+ `apply`
+ `bind`

4. `new`绑定

使用`new`操作符会执行下面的操作
1. 创建(或者说构造)一个全新的对象。
2. 这个新对象会被执行[[原型]]连接。
3. 这个新对象会绑定到函数调用的`this`。
4. 如果函数没有返回其他对象，那么`new`表达式中的函数调用会自动返回这个新对象。

**间接引用**

有可能创建一个函数的“间接引用”，这种情况下调用这个函数会应用默认绑定规则。

容易在赋值时发生

```js
function foo() {
    console.log(this.a);
}

var a = 2;
var o = { a: 3, foo: foo };
var p = { a: 4 };

o.foo(); //3
(p.foo = o.foo)(); //2
```

赋值表达式`p.foo = o.foo`返回值是目标函数的引用，因此这里会应用默认绑定。

**ES6箭头函数**

```js
function foo() {
    //返回一个箭头函数
    return (a) => {
        //this继承自foo()
        console.log( this.a );
    };
}

var obj1 = {
    a:2
};

var obj2 = {
    a:3
};

var bar = foo.call( obj1 );
bar.call( obj2 ); //2，不是 3!!!
```

箭头函数的绑定无法被修改

## chap3. 对象

### 3.1 语法

对象可以通过两种方式定义：声明（字面量）和构造形式。

字面量形式语法是这样：

```js
var myObj = {
    key: value
    // ...
};
```

构造形式：

```js
var myObj = new Object();
myObj.key = value;
```

两种方式生成的对象是一样的，区别是字面量可以定义多个键值对，构造形式必须逐个添加。

### 3.2 类型

JS中有六种主要类型
1. `string`
2. `number`
3. `boolean`
4. `null`
5. `undefined`
6. `object`

`null`会被当做对象是语言本身的一个bug,`typeof null`会返回字符串`"object"`。

#### 内置对象

1. `String`
2. `Number`
3. `Boolean`
4. `Object`
5. `Function`
6. `Array`
7. `Date`
8. `RegExp`
9. `Error`

```js
var strPrimitive = "I am a string";
typeof strPrimitive; // "string"
strPrimitive instaneof String; // false

var strObject = new String( "I am a string" );
typeof strObject; // "object"
strObject instanceof String; // true
```

> 直接在字符串字面量上访问属性或者方法是因为引擎自动把字面量转换成对象，所以可以访问属性和方法。

`number`和`boolean`字面量也是如此

`null`和`undefined`没有对应的构造形式

相反`Date`只有构造形式没有字面量形式

对于`Object`、`Array`、`Function`、`RegExp`来说，无论使用文字还是构造形式它们都是对象。

### 3.3 内容

```js
var myObject = {
    a: 2
};

myObject.a; // 2

myObject["a"]; // 2
```

访问`myObject`中`a`位置上的值，使用`.`操作符或者`[]`操作符.

`.`操作符通常被称为“属性访问”

`[]`操作符通常被称为“键访问”

他们访问的是同一个位置

属性名永远都是字符串，如果使用`string`(字面量)以外的其他值作为属性名，那么它首先会被转换为一个字符串。

#### 3.3.1 可计算属性名

ES6增加了可计算属性名，可以在文字形式中使用`[]`包裹一个表达式来当做属性名：

```js
var prefix = "foo";

var myObject = {
    [prefix + "bar"]: "hello",
    [prefix + "baz"]: "world",
};

myObject["foobar"]; // hello
myObject["foobaz"]; // world
```

#### 3.3.2 属性与方法

> 函数永远不会“属于”一个对象

属性访问返回的函数和其他函数没有任何区别（除了可能发生的隐式绑定`this`）

即使子对象字面量中声明了一个函数表达式，这个函数也不会“属于”这个对象---它们只是对于相同函数对象的多个引用。

```js
var myObject = {
    foo: function() {
        console.log( "foo" );
    }
};

var someFoo = myObject.foo;

someFoo; // function foo(){..}

myObject.foo; // function foo(){..}
```

#### 3.3.3 数组

##### 深复制和浅复制

> 浅复制是对对象地址的复制，并没有开辟新的栈，也就是复制的结果是两个对象指向同一个地址，修改其中一个对象的属性，则另一个对象的属性也会改变，而深复制则是开辟新的栈，两个对象对应两个不同的地址，修改一个对象的属性，不会改变另一个对象的属性。

