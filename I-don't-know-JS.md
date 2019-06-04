# I don't know JS

## chap1. 作用域

### 1.1 编译原理

> 尽管通常将 JavaScript 归类为“动态”或“解释执行”语言，但事实上它是一门编译语言。

传统编译语言代码执行之前经历三个步骤

1.分词/词法分析(Tokenizing/Lexing)
这个过程将字符组成的字符串分解成有意义的代码块(token)，比如`var a = 2;`通常被分解为`var`、`a`、`=`、`2`、`;`

2.解析/语法分析(Parsing)
将词法单元流转化弄成一个结构树。抽象语法树(Abstract Syntax Tree，AST)

3.代码生成
将 AST 转化为一组机器指令

### 1.2 理解作用域

1.引擎

从头到尾负责整个`JavaScript`程序的编译及执行过程。

2.编译器

引擎的好朋友之一，负责语法分析及代码生成等脏活累活。

3.作用域

引擎的另一位好朋友，负责收集并维护由所有声明的标识符(变量)组成的一系列查询，并实施一套非常严格的规则，确定当前执行的代码对这些标识符的访问权限。

以`var a = 2;`为例，说明编译器、引擎和作用域如何协同工作。

编译器会进行如下处理

1. 遇到`var a`，编译器会询问作用域是否已经有一个该名称的变量存在于同一个作用域的集合中。如果是，编译器会忽略该声明，继续进行编译;否则它会要求作用域在当前作用域的集合中声明一个新的变量，并命名为`a`。

1. 接下来编译器会为引擎生成运行时所需的代码，这些代码被用来处理`a = 2`这个赋值操作。引擎运行时会首先询问作用域，在当前的作用域集合中是否存在一个叫作`a`的变量。如果是，引擎就会使用这个变量;如果否，引擎会继续查找该变量。

> 总结:变量的赋值操作会执行两个动作，首先编译器会在当前作用域中声明一个变量(如果之前没有声明过)，然后在运行时引擎会在作用域中查找该变量，如果能够找到就会对它赋值。

#### LHS 和 RHS 查询

- LHS-Left Hand Side
- RHS-Right Hand Side

当变量出现在赋值操作的左侧时进行`LHS`查询，出现在右侧时进行`RHS`查询。`RHS`查询与简单地查找某个变量的值别无二致，而`LHS`查询则是试图找到变量的容器本身，从而可以对其赋值。

`console.log( a );`这里对`a`的引用是`LHS`引用。

下面的程序既有`LHS`引用也有`RHS`引用

```javascript
function foo(a) {
  console.log(a); // 2
}
foo(2);
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

| 查询     | 类别 |
| -------- | ---- |
| `foo(2)` | RHS  |
| `a=2`    | LHS  |
| `a`      | RHS  |
| `b`      | LHS  |
| `a`      | RHS  |
| `b`      | RHS  |
| `c`      | LHS  |

### 1.3 作用域嵌套

> 当一个块或函数嵌套在另一个块或函数中时，就发生了作用域的嵌套。因此，在当前作用域中无法找到某个变量时，引擎就会在外层嵌套的作用域中继续查找，直到找到该变量，或抵达最外层的作用域(也就是全局作用域)为止。

```javascript
function foo(a) {
  console.log(a + b);
  b = a;
}
foo(2);
```

第一次对`b`进行`RHS`查询时是无法找到该变量的。也就是说，这是一个“未声明”的变量，因为在任何相关的作用域中都无法找到它。

如果`RHS`查询在所有嵌套的作用域中遍寻不到所需的变量，引擎就会抛出`ReferenceError`异常。值得注意的是，`ReferenceError`是非常重要的异常类型。

如果`RHS`查询找到了一个变量，但是你尝试对这个变量的值进行不合理的操作，比如试图对一个非函数类型的值进行函数调用，或着引用`null`或`undefined`类型的值中的属性，那么引擎会抛出另外一种类型的异常，叫作`TypeError`。

> `ReferenceError`同作用域判别失败相关，而`TypeError`则代表作用域判别成功了，但是对结果的操作是非法或不合理的。

### 总结一下

- 变量（标识符）
- 如果查找的目的是对变量进行赋值，那么就会使用`LHS`查询;如果目的是获取变量的值，就会使用`RHS`查询。

## chap 2.词法作用域

### 2.1 词法阶段

> 无论函数在哪里被调用，也无论它如何被调用，它的词法作用域都只由函数被声明时所处的位置决定。

### 2.2 欺骗词法

`JavaScript`中的`eval(..)`函数可以接受一个字符串为参数，并将其中的内容视为好像在书写时就存在于程序中这个位置的代码。换句话说，可以在你写的代码中用程序生成代码并运行，就好像代码是写在那个位置的一样。

#### `eval`

```javascript
function foo(str, a) {
  eval(str); // 欺骗!
  console.log(a, b);
}
var b = 2;
foo("var b = 3;", 1); // 1, 3
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

VO 按照如下顺序填充：

1. 函数参数
1. 函数声明
1. 变量声明

> 我们习惯将`var a = 2;`看作一个声明，而实际上`JavaScript`引擎并不这么认为。它将`var a`
> 和`a = 2`当作两个单独的声明，第一个是编译阶段的任务，而第二个则是执行阶段的任务。

## chap5. 作用域闭包

> 本质上无论何时何地，如果将函数(访问它们各自的词法作用域)当作第一级的值类型并到处传递，你就会看到闭包在这些函数中的应用。在定时器、事件监听器、 Ajax 请求、跨窗口通信、Web Workers 或者任何其他的异步(或者同步)任务中，只要使用了回调函数，实际上就是在使用闭包!

```javascript
for (var i = 1; i <= 5; i++) {
  (function(j) {
    setTimeout(function timer() {
      console.log(j);
    }, j * 1000);
  })(i);
}
```

在这个经典的闭包例子中，使用 IIFE 的目的是为了创建一个"块级"作用域，用来保存 i。

## 附录 A 动态作用域

```javascript
function foo() {
  console.log(a); // 2
}
function bar() {
  var a = 3;
  foo();
}
var a = 2;
bar();
```

词法作用域让`foo()`中的`a`通过`RHS`引用到了全局作用域中的`a`，因此会输出 2。

动态作用域并不关心函数和作用域是如何声明以及在何处声明的，只关心它们从何处调用。换句话说，作用域链是基于调用栈的，而不是代码中的作用域嵌套。

> 主要区别:词法作用域是在写代码或者说定义时确定的，而动态作用域是在运行时确定的。(`this`也是!)词法作用域关注函数在何处声明，而动态作用域关注函数从何处调用。

需要明确的是，事实上`JavaScript`并不具有动态作用域。它只有词法作用域，简单明了。但是`this`机制某种程度上很像动态作用域。

## 附录 C `this`词法

## `this`和对象原型

## chap1. 关于`this`

`this`实际上是在函数被调用时发生的绑定，它的指向完全取决于函数在哪里被调用。

## chap2. `this`全面解析

## 个人理解

`this`是什么?

`this`是函数内部的一个对象，`this`引用的是函数执行的环境对象。

### 2.1 调用位置

> 最重要的就是分析调用栈，我们关心的调用位置就在当前正在执行的函数的前一个调用中。因为函数的`this`是在执行上下文的`Creation Stage`中确定的。

```javascript
function baz() {
  // 当前调用栈是:baz
  // 因此，当前调用位置是全局作用域
  console.log("baz");
  bar(); // <-- bar的调用位置
}
function bar() {
  // 当前调用栈是 baz -> bar
  // 因此，当前调用位置在 baz 中
  console.log("bar");
  foo(); // <-- foo的调用位置
}
function foo() {
  // 当前调用栈是 baz -> bar -> foo
  // 因此，当前调用位置在 bar 中
  console.log("foo");
}
baz(); // <-- baz的调用位置
```

### 2.2 绑定规则

1.默认绑定

独立函数调用 --> 默认绑定

```javascript
function foo() {
  console.log(this.a);
}
var a = 2;
foo(); //2
```

`foo()`是直接使用不带任何修饰的函数引用进行调用的，因此只能使用默认绑定，无法应用其他规则。

2.隐式绑定

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

#### 隐式丢失

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

3.显式绑定

- `call`
- `apply`
- `bind`

  4.`new`绑定

使用`new`操作符会执行下面的操作

1. 创建(或者说构造)一个全新的对象。
1. 这个新对象会被执行[[原型]]连接。
1. 这个新对象会绑定到函数调用的`this`。
1. 如果函数没有返回其他对象，那么`new`表达式中的函数调用会自动返回这个新对象。

#### 间接引用

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

#### ES6 箭头函数

```js
function foo() {
  //返回一个箭头函数
  return a => {
    //this继承自foo()
    console.log(this.a);
  };
}

var obj1 = {
  a: 2
};

var obj2 = {
  a: 3
};

var bar = foo.call(obj1);
bar.call(obj2); //2，不是 3!!!
```

箭头函数的绑定无法被修改

再来看看`MDN`上对`this`的一些补充

全局上下文(在任何函数体外部)的变量对象就是全局对象,`this`都指代全局对象。

函数内部则取决于调用的方式，这与上文所提到相同。

> 如果要想把`this`的值从一个上下文传到另一个，就要用`call`，或者`apply`方法。

当函数作为对象里的方法被调用时，它们的`this`是调用该函数的对象。`this`的绑定只受最靠近的成员引用的影响。

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

JS 中有六种主要类型

1. `string`
1. `number`
1. `boolean`
1. `null`
1. `undefined`
1. `object`

`null`会被当做对象是语言本身的一个 bug,`typeof null`会返回字符串`"object"`。

#### 内置对象

1. `String`
1. `Number`
1. `Boolean`
1. `Object`
1. `Function`
1. `Array`
1. `Date`
1. `RegExp`
1. `Error`

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

`null`和`undefed`没有对应的构造形式

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

ES6 增加了可计算属性名，可以在文字形式中使用`[]`包裹一个表达式来当做属性名：

```js
var prefix = "foo";

var myObject = {
  [prefix + "bar"]: "hello",
  [prefix + "baz"]: "world"
};

myObject["foobar"]; // hello
myObject["foobaz"]; // world
```

#### 3.3.2 属性与方法

> 函数永远不会“属于”一个对象

属性访问返回的函数和其他函数没有任何区别（除了可能发生的隐式绑定`this`）

即使子对象字面量中声明了一个函数表达式，这个函数也不会“属于”这个对象---它们只是对于相同函数对象的多个引用。

```js
var myObject = {
  foo: function() {
    console.log("foo");
  }
};

var someFoo = myObject.foo;

someFoo; // function foo(){..}

myObject.foo; // function foo(){..}
```

#### 3.3.4 复制对象

##### 深复制和浅复制

> 浅复制是对对象地址的复制，并没有开辟新的栈，也就是复制的结果是两个对象指向同一个地址，修改其中一个对象的属性，则另一个对象的属性也会改变，而深复制则是开辟新的栈，两个对象对应两个不同的地址，修改一个对象的属性，不会改变另一个对象的属性。

对于 JSON 安全的对象来说，有一种巧妙的复制方法：

```js
var newObj = JSON.parse(JSON.stringify(someObj));
```

#### 3.3.5 属性描述符

ES5 开始，所有的属性都具备了属性描述符

```js
var myObject = {
  a: 2
};

Object.getOwnPropertyDescriptor(myObject, "a");
//{
//  value: 2,
//  writable: true,
//  enumerable: true,
//  configurable: true
//}
```

创建普通属性时属性描述符会使用默认值，可以使用`Object.defineProperty(..)`来添加新属性或者修改已有属性并对特性进行设置。

```js
var myObject = {};

Object.defineProperty(myObject, "a", {
  value: 2,
  writable: true,
  configurable: true,
  enumerable: true
});

myObject.a; // 2
```

1.`Writable`

`Writable`决定是否可以修改属性的值。

2.`Configurable`

只要属性是可配置的，就可以使用`defineProperty(..)`方法来修改属性描述符：

把`configurable`修改成`false`是单向操作，无法撤销!

> 要注意有一个小小的例外:即便属性是 `configurable:false`，我们还是可以把`writable`的状态由`true`改为`false`，但是无法由`false`改为`true`。

除了无法修改，`configurable:false`还会禁止删除这个属性。

3.`Enumerable`

`for ..in`循环中，如果把`enumerable`设置成`false`，这个属性就不会出现在枚举中。

#### 3.3.6 不变性

有时候会希望属性或者对象不可改变

所有方法创建的都是浅不变性，他们只会影响目标对象和它的直接属性，如果目标引用了其他对象，其他对象的内容不受影响，仍然可变。

1.对象常量
结合`writeble:false`和`configurable:false`就可以创建一个真正的常量属性（不可修改、重定义或者删除）：

```js
var myObject = {};

Object.defineProperty(myObject, "FAVORITE_NUMBER", {
  value: 42,
  writeble: false,
  configurable: false
});
```

2.禁止扩展

禁止一个对象添加新属性并且保留已有属性，可以使用`Object.preventExtensions(..)`:

```js
var myObject = {
  a: 2
};

Object.preventExtensions(myObject);

myObject.b = 3;
myObject.b; // undefined
```

3.密封

`Object.seal(..)`会创建一个“密封”的对象，这个方法实际上会在一个现有的对象上调用`Object.precentExtensions(..)`并把所有现有属性标记为`configurable:false`。

密封之后不仅不能添加新属性，也不能重新配置或者删除任何现有属性（虽然可以修改属性的值）。

4.冻结

`Object.freeze(..)`会创建一个冻结对象，这个方法实际上会在一个现有对象上调用`Object.seal(..)`并把所有“数据访问”属性标记为`writable:false`，这样就无法修改它们的值。

#### 3.3.7 [[Get]]

```js
var myObject = {
  a: 2
};

myObject.a; // 2
```

`myObject.a`是一次属性访问，但不仅仅是在`myObject`中查找名字为`a`的属性，在语言规范中，`myObject.a`在`myObject`上实际上实现了`[[GET]]`操作。

对象默认的内置`[[Get]]`操作首先在对象中查找是否有名称相同的属性， 如果找到就会返回这个属性的值。

如果没有找到名称相同的属性，按照`[[GET]]`算法的定义会遍历可能存在的`[[Prototype]]`链，也就是原型链。

如果无论如何都没有找到名称相同的属性，那么`[[GET]]`操作会返回值`undefined`:

```js
var myObject = {
  a: 2
};

myObject.b; // undefined
```

当访问当前词法作用域中不存在的变量，会抛出`ReferenceError`异常

#### 3.3.8 [[Put]]

与`[[GET]]`操作相对应

如果对象中已经存在这个属性，`[[Put]]`算法大致检查下面这些内容。

1. 属性是否是访问描述符(参见 3.3.9 节)?如果是并且存在`setter`就调用`setter`。
1. 属性的数据描述符中`writable`是否是`false`?如果是，在非严格模式下静默失败，在严格模式下抛出`TypeError`异常。
1. 如果都不是，将该值设置为属性的值。

如果对象中不存在这个属性，[[Put]]操作更加复杂

#### 3.3.9 Getter 和 Setter

当给一个属性定义`getter`、`setter`或者两者都有时，这个属性会被定义为“访问描述符”（和“数据描述符”相对）。对于访问描述符来说，JS 会忽略他们的`value`和`writeble`特性，取而代之的是关心`set`和`get`（还有`configurable`和`enumerable`）特性。

通常来说`getter`和`setter`是成对出现的。

```js
var myObject = {
  // 给a定义一个getter
  get a() {
    return this._a_;
  },

  // 给a定义一个setter
  set a(val) {
    this._a_ = val * 2;
  }
};

myObject.a = 2;
myObject.a; // 4
```

#### 3.3.10 存在性

`myObject.a`的属性访问返回值可能是`undefined`，但是这个值有可能是属性中存储的`undefined`，也可能是因为属性不存在所以返回`undefined`。

```js
var myObject = {
  a: 2
};

"a" in myObject; // true
"b" in myObject; // false

myObject.hasOwnProperty("a"); // true
myObject.hasOwnProperty("b"); // false
```

`in`操作符会检查属性是否在对象及其[[Prototype]]原型链中，`hasOwnProperty(..)`只会检查属性是否在`myObject`对象中，不会检查[[Prototype]]链。

> 在数组上应用`for ..in`循环有时会产生出人意料的结果，最好只在对象上应用`for ..in`循环，如果要遍历数组就使用传统的`for`循环来遍历数组索引。

```js
var myObject = {};

Object.defineProperty(
  myObject,
  "a",
  // 让a像普通属性一样可以枚举
  { enumerable: true, value: 2 }
);

Object.defineProperty(
  myObject,
  "b",
  // 让b不可枚举
  { enumerable: false, value: 3 }
);

myObject.propertyIsEnumerable("a"); // true
myObject.propertyIsEnumerable("b"); // false

Object.keys(myObject); // ["a"]
Object.getOwnPropertyNames(myObject); // ["a", "b"]
```

`propertyIsEnumerable(..)`会检查给定的属性名是否直接存在于对象中(而不是在原型链上)并且满足`enumerable:true`。

`Object.keys(..)`会返回一个数组，包含所有可枚举属性，`Object.getOwnPropertyNames(..)`会返回一个数组，包含所有属性，无论它们是否可枚举。

`in`和`hasOwnProperty(..)`的区别在于是否查找[[Prototype]]链，然而，`Object.keys(..)`和`Object.getOwnPropertyNames(..)`都只会查找对象直接包含的属性。

### 3.4 遍历

> 遍历对象属性时的顺序是不确定的，不同`JavaScript`引擎可能不一样。

ES6 增加的`for ..of`循环语法直接遍历数组的值（如果对象本身定义了迭代器的话也可以遍历对象）：

```js
var myArray = [1, 2, 3];

for (var v of myArray) {
  console.log(v);
}
// 1
// 2
// 3
```

JavaScript 中的对象有字面形式(比如`var a = { .. }`)和构造形式(比如`var a = new Array(..)`)。字面形式更常用，不过有时候构造形式可以提供更多选项。

对象就是键/值对的集合。可以通过`.propName`或者`["propName"]`语法来获取属性值。访问属性时，引擎实际上会调用内部的默认`[[Get]]`操作(在设置属性值时是`[[Put]])，[[Get]]`操作会检查对象本身是否包含这个属性，如果没找到的话还会查找`[[Prototype]]`链。

属性的特性可以通过属性描述符来控制，比如`writable`和`configurable`。此外，可以使用`Object.preventExtensions(..)`、`Object.seal(..)`和`Object.freeze(..)`来设置对象(及其 属性)的不可变性级别。

属性不一定包含值——它们可能是具备`getter/setter`的“访问描述符”。此外，属性可以是可枚举或者不可枚举的，这决定了它们是否会出现在`for..in`循环中。

你可以使用 ES6 的`for..of`语法来遍历数据结构(数组、对象，等等)中的值，`for..of`会寻找内置或者自定义的`@@iterator`对象并调用它的`next()`方法来遍历数据值。

## Chap4. 混合对象“类”

面向类的设计模式：

1. 实例化(instantitaion)
1. 继承(inheritance)
1. 多态(polymorphism)

### 4.1 类理论

> 面向对象编程强调的是数据和操作数据的行为本质上是相互关联的，好的设计就是把数据以及和它相关的行为封装起来。

举一个例子，“汽车”可以被看做“交通工具”的一种特例。

在软件中定义一个`Vehicle`类和一个`Car`类来对这种关系进行建模。

`Vehicle`的定义可能包含推进器（比如引擎）、载人能力等等。

定义`Car`时，只要声明它继承（或者扩展）了`Vehicle`的基础定义即可。`Car`的定义就是对通用`Vehicle`定义的特殊化。

虽然`Vehicle`和`Car`会定义相同的方法，但是实例中的数据可能是不同的，比如每辆车独一无二的 VIN(Vehicle Identification Number)。

这便是类、继承和实例化。

类的另一个核心概念是多态，父类的通用行为可以被子类用更特殊的行为重写。

#### 4.1.1 “类”设计模式

面向对象设计模式包括：迭代器模式、观察者模式、工厂模式、单例模式，等等。

还有过程化编程和函数式编程。

#### 4.1.2 JavaScript 中的类

简单来说`JavaScript`中没有真正意义上的“类”。`JavaScript`提供了一些近似类的语法满足对于类设计模式的最普通需求。

### 4.2 类的机制

#### 4.2.1 建造

“类” 和“实例”的概念源于房屋建造。

建筑是蓝图的物理实例，本质上是对蓝图的复制。

#### 4.2.2 构造函数

类构造函数通常和类同名，需要使用`new`来调用，这样语言引擎才知道你想要构造一个新的类实例。

### 4.3 类的继承

定义好一个子类之后，相对于父类来说它就是一个独立并且完全不同的类。子类会包含父类行为的原始副本，也可以重写所有继承的行为甚至定义新行为。

关于类继承的伪代码

```js
class Vehicle {
    engines = 1;

    ignition() {
        output( "Turning on my engine." );
    }

    drive() {
        ignition();
        output( "Steering and moving forward!" );
    }
}

class Car inherits Vehicle {
    wheels = 4;

    drive() {
        inherited:drive()
        output( "Rolling on all ", wheels, " wheels!" );
    }
}

class SpeedBoat inherits Vehicle {
    engines = 2;

    ignition() {
        output( "Turning on my ", engines, " engines." );
    }

    pilot() {
        inherited:drive()
        output( "Speeding through the water with ease!" );
    }
}
```

#### 4.3.1 多态

`Car`重写了继承自父类的`drive()`方法，但是之后`Car`调用了`inherited:drive()`方法，这表明`Car`可以引用继承来的原始`drive()`方法。快艇的`pilot()`方法同样引用了原始`drive()`方法。

这种技术被称为多态或者虚拟多态。

我们不会定义想要访问的绝对继承层次（或者说类），而是使用相对引用“查找上一层”。

多态的另一个方面是，在继承链的不同层次中的一个方法名可以被多次定义，当调用方法时会自动选择合适的定义。

`SpeedBoat`中的`pilot()`通过相对多态引用了`Vehicle`中的`drive()`。但是那个`drive()`方法直接通过名字引用了`ignotion()`方法。

本例中语言引擎会使用`SpeedBoat`的`ignition()`，如果直接实例化`Vehicle类`然后调用它的`drive()`，那么语言引擎就会使用`Vehicle`中的`ignition()`方法。

> 从概念上说，子类可以通过相对多态引用(或者说 super)来访问父类中的行为。子类得到的仅仅是继承自父类行为的一份副本。子类对继承到的一个方法进行“重写”，不会影响父类中的方法，这两个方法互不影响，因此才能使用相对多态引用访问父类中的方法(如果重写会影响父类的方法，那重写之后父类中的原始方法就不存在了，自然也无法引用)。

多态并不表示子类和父类有关联，子类的到的是父类的一份副本。类的继承其实就是复制。

#### 4.3.2 多重继承

多重继承意味着所有父类的定义都会被复制到子类中。

如果两个父类都定义了`drive()`方法，子类引用谁？

还有一种被称为`钻石问题`。在钻石问题中，子类`D`继承自两个父类(`B`和`C`)，这两个父类都继承自`A`。如果`A`中有`drive()`方法并且`B`和`C`都重写了这个方法(多态)，那当`D`引用`drive()`时应当选择哪个版本呢(`B:drive()`还是`C:drive()`)？

### 4.4 混入

> 在继承或者实例化时，JavaScript 的对象机制并不会自动执行复制行为。简单来说， JavaScript 中只有对象，并不存在可以被实例化的“类”。

于是有了混入来模拟类的复制行为。

有两种类型的混入:显式和隐式。

#### 4.4.1 显式混入

1.显式混入

```js
// 非常简单的 mixin(..) 例子 :
function mixin(sourceObj, targetObj) {
  for (var key in sourceObj) {
    // 只会在不存在的情况下复制
    if (!(key in targetObj)) {
      targetObj[key] = sourceObj[key];
    }
  }
  return targetObj;
}
var Vehicle = {
  engines: 1,
  ignition: function() {
    console.log("Turning on my engine.");
  },
  drive: function() {
    this.ignition();
    console.log("Steering and moving forward!");
  }
};
var Car = mixin(Vehicle, {
  wheels: 4,
  drive: function() {
    Vehicle.drive.call(this);
    console.log("Rolling on all " + this.wheels + " wheels!");
  }
});
```

> 我们处理的已经不再是类了，因为在`JavaScript`中不存在类，`Vehicle`和`Car`都是对象，供我们分别进行复制和粘贴。

`Vehicle.drive.call(this)`这就是显式多态，之前的伪代码`inherited:drive()`称之为相对多态。

2.混合复制

```js
// 另一种混入函数，可能有重写风险
function mixin(sourceObj, targetObj) {
  for (var key in sourceObj) {
    targetObj[key] = sourceObj[key];
  }
  return targetObj;
}

var Vehicle = {
  // ...
};

// 首先创建一个空对象并把 Vehicle 的内容复制进去
var Car = mixin(Vehicle, {});

// 然后把新内容复制到 Car 中
mixin(
  {
    wheels: 4,
    drive: function() {
      // ...
    }
  },
  Car
);
```

复制完成之后`Car`和`Vehicle`就分离了，但是两者要是引用同一个对象还是会相互影响对方。

还有寄生继承和隐式混入，详见 p138

### 4.5 小结

`JavaScript`中的类与其他语言中的类完全不同。

类意味着复制。

传统的类被实例化时，它的行为会被复制到实例中。类被继承时，行为也会被复制到子类中。

多态(在继承链的不同层次名称相同但是功能不同的函数)看起来似乎是从子类引用父类，但是本质上引用的其实是复制的结果。

总的来说，在`JavaScript`中模拟类是得不偿失的，虽然能解决当前的问题，但是可能会埋下更多的隐患。

## 5. 原型

### 5.1 [[Prototype]]

```js
var anotherObject = {
  a: 2
};

//创建一个关联到anotherObject的对象
var myObject = Object.create(anotherObject);

myObject.a; // 2
```

使用`for..in`遍历对象时原理和查找`[[prototype]]`链类似，任何可以通过原型链访问到(并且是`enumerable`)的属性都会被枚举。使用`in`操作符来检查属性在对象中是否存在时同样会查找对象的整条原型链(无论属性是否可枚举)。

#### 5.1.1 Object.prototype

所有普通的`[[Prototype]]`链最终都会指向内置的`Object.prototype`。

比如`.toString()`和`.valueOf()`方法，还有`.hasOwnProperty(..)`、`.isPrototypeOf(..)`方法，都是`Object.prototype`中包含的功能。

#### 5.1.2 属性设置和屏蔽

```js
myObject.foo = "bar";
```

如果`myObject`对象中包含名为`foo`的普通数据访问属性，这条赋值语句只会修改已有的属性值。

如果`foo`不是直接存在于`myObject`中，[[Prototype]]链就会被遍历，类似[[Get]]操作。如果原型链上找不到`foo`，`foo`就会被直接添加到`myObject`上。

如果属性名`foo`既出现在`myObject`中也出现在`myObject`的[[Prototype]]链上层，那么就会发生屏蔽。`myObject`中包含的`foo`属性会屏蔽原型链上层的所有`foo`属性，因为`myObject.foo`总是会选择原型链中最底层的`foo`属性。

当`foo`不直接存在于`myObject`中而是存在于原型链上层时`myObject.foo = "bar"`会出现三种情况。

1.如果在`[[Prototype]]`链上层存在名为`foo`的普通数据访问属性并且没有标记为只读(`writable:false`)，那就会直接在`myObject`中添加一个名为`foo`的新属性，它是屏蔽属性。

2.如果在`[[Prototype]]`链上层存在`foo`，但是它被标记为只读(`writable:false`)，那么无法修改已有属性或者在`myObject`上创建屏蔽属性。如果在严格模式下，代码会抛出一个错误。否则这条赋值语句会被忽略。总之，不会发生屏蔽。

3.如果在`[[Prototype]]`链上层存在`foo`并且它是一个`setter`，那就一定会调用这个`setter`。`foo`不会被添加到(或者说屏蔽于)`myObject`，也不会重新定义`foo`这个`setter`。

大多数开发者都认为如果向`[[Prototype]]`链上层已经存在的属性(`[[Put]]`)赋值，就一定会触发屏蔽，实际情况是三种属性只有第一种是这样的。

如果希望在第二种和第三种情况下屏蔽`foo`，那就不能使用`=`操作符来赋值，而是使用`Object.defineProperty(..)`来向`myObject`添加`foo`。

> 第二种情况可能是最令人意外的，只读属性会阻止`[[Prototype]]`链下层隐式创建(屏蔽)同名属性。这样做主要是为了模拟类属性的继承。你可以把原型链上层的`foo`看作是父类中的属性，它会被`myObject`继承(复制)，这样一来`myObject`中的`foo`属性也是只读，所以无法创建。但是一定要注意，实际上并不会发生类似的继承复制(参见第 4 章和第 5 章)。这看起来有点奇怪，`myObject`对象竟然会因为其他对象中有一个只读`foo`就不能包含`foo`属性。更奇怪的是，这个限制只存在于`=`赋值中，使用`Object.defineProperty(..)`并不会受到影响。第二种情况可能是最令人意外的，只读属性会阻止`[[Prototype]]`链下层隐式创建(屏蔽)同名属性。这样做主要是为了模拟类属性的继承。你可以把原型链上层的`foo`看作是父类中的属性，它会被`myObject`继承(复制)，这样一来`myObject`中的`foo`属性也是只读，所以无法创建。但是一定要注意，实际上并不会发生类似的继承复制(参见第 4 章和第 5 章)。这看起来有点奇怪，`myObject`对象竟然会因为其他对象中有一个只读`foo`就不能包含`foo`属性。更奇怪的是，这个限制只存在于`=`赋值中，使用`Object.defineProperty(..)`并不会受到影响。

下面代码会发生隐式屏蔽

```js
var anotherObject = {
  a: 2
};

var myObject = Object.create(anotherObject);

another.a; // 2
myObject.a; // 2

anotherObject.hasOwnProperty("a"); // true
myObject.hasOwnProperty("a"); // false

myObject.a++; // 隐式屏蔽！

anotherObject.a; // 2
myObject.a; // 3

myObject.hasOwnProperty("a"); // true
```

`++`操作符相当于`myObject.a = myObject.a + 1`。`++`操作符首先会通过`[[Protitype]]`查找属性`a`并从`anotherObject.a`获取当前属性值 2，然后给这个值加 1，接着用`[[Put]]`将值 3 赋给`myObject`中新建的屏蔽属性`a`。

### 5.2 “类”

`JavaScript`和面向类的语言不通，它并没有类来作为对象的抽象模式。`JavaScript`中只有对象。

实际上`JavaScript`才是真正应该被称为“面向对象”的语言，因为它是少有的可以不通过类，直接创建对象的语言。

#### 5.2.1 “类”函数

所有的函数默认都会拥有一个名为`prototype`的公有并且不可枚举的属性，它会指向另一个对象：

```js
function Foo() {
  // ...
}

Foo.prototype; // { }
```

这个对象通常被称为`Foo`的原型，因为我们通过名为`Foo.prototype`的属性引用来访问它。

这个对象是在调用`new Foo()`时创建的，最后会被关联到`Foo.prototype`对象上。

```js
function Foo() {
  // ...
}

var a = new Foo();

Object.getPrototypeOf(a) === Foo.prototype; // true
```

回顾一下`new`操作：

1. 创建(或者说构造)一个全新的对象。
1. 这个新对象会被执行[[原型]]连接。
1. 这个新对象会绑定到函数调用的`this`。
1. 如果函数没有返回其他对象，那么`new`表达式中的函数调用会自动返回这个新对象。

调用`new Foo()`时会创建 a，其中的一步就是给 a 一个内部的`[[Prototype]]`链接，关联到`Foo.prototype`指向的那个对象。

面向类的语言中，实例化（或者继承）一个类就意味着“把类的行为复制到物理对象中”，对于每一个新实例来说都会重复这个过程。

在`JavaScript`中我们没有初始化一个类，只是让两个对象相互关联，`JavaScript`中只能创建多个对象，它们的`[[Prototype]]`关联的是同一个对象。

##### 关于名称

从视觉角度来说，`[[Prototype]]`机制如下图所示。

![prototype机制](http://ovyn493ey.bkt.clouddn.com/prototype.png)

这个机制通常被称为原型继承，它常常被视为动态语言版本的类继承。

继承意味着复制操作，`JavaScript`(默认)并不会复制对象属性。相反，`JavaScript`会在两个对象之间创建一个关联，这样一个对象就可以通过委托访问另一个对象的属性和函数。委托(参见第 6 章)这个术语可以更加准确地描述`JavaScript`中对象的关联机制。

#### “构造函数”

## chap3. 类型和语法

### 类型

ES5.1 规范中的运算法则所操纵的值均有相应的类型。

我们这样来定义“类型”(与规范类似):对语言引擎和开发人员来说，类型是值的内部特征，它定义了值的行为，以使其区别于其他值。

#### 内置类型

- 空值(null)
- 未定义(undefined)
- 布尔值(boolean)
- 数字(number)
- 字符串(string)
- 对象(object)
- 符号(symbol，ES6 中新增)

```js
typeof null === "object"; // true
```

正确的返回结果应该是`"null"`，但这个`bug`由来已久，在`JavaScript`中已经存在了将近二十年，也许永远也不会修复，因为这牵涉到太多的`Web`系统，“修复”它会产生更多的`bug`，令许多系统无法正常工作。

我们需要使用复合条件来检测`null`值的类型:

```js
var a = null;
!a && typeof a === "object"; // true
```

```js
typeof function a() {
  /* .. */
} === "function"; // true
```

函数实际上是`object`的一个"子类型"，具体来说，函数是可调用的对象，有一个内部属性`[[Call]]`，该属性使其可以被调用。

函数可以拥有属性。

```js
function a(b, c) {
  /* .. */
}

a.length; // 2
```

函数对象的 length 属性是其声明的参数的个数。

#### 值和类型

`JavaScript`中的变量是没有类型的，只有值才有。变量可以随时持有任何类型的值。

在对变量执行`typeof`操作时，得到的结果并不是该变量的类型，而是该变量持有的值的类型，因为`JavaScript`中的变量没有类型。

##### `undefined`和`undeclared`

已在作用域中声明但还没有赋值的变量，是`undefined`的。相反，还没有在作用域中声明过的变量，是`undeclared`的。

```js
var a;
a; // undefined
b; // ReferenceError: b is not defined
```

```js
var a;
typeof a; // "undefined"
typeof b; // "undefined"
```

与`undeclared`变量不同，访问不存在的对象属性(甚至是在全局对象`window`上)不会产生`ReferenceError`错误。

##### 小结

`JavaScript`有七种内置类型:`null`、`undefined`、`boolean`、`number`、`string`、`object`和`symbol`，可以使用`typeof`运算符来查看。变量没有类型，但它们持有的值有类型。类型定义了值的行为特征。

很多开发人员将`undefined`和`undeclared`混为一谈，但在`JavaScript`中它们是两码事。`undefined`是值的一种。`undeclared`则表示变量还没有被声明过。

遗憾的是，`JavaScript`却将它们混为一谈，在我们试图访问`"undeclared"`变量时这样报错:`ReferenceError: a is not defined`，并且`typeof`对`undefined`和`undeclared`变量都返回`"undefined"`。

然而，通过`typeof`的安全防范机制(阻止报错)来检查`undeclared`变量，有时是个不错的办法。

### 值

#### 数组

数组通过数字进行索引，但有趣的是它们也是对象，所以也可以包含字符串键值和属性 (但这些并不计算在数组长度内):

```js
var a = [];

a[0] = 1;
a["foobar"] = 2;

a.length; // 1
a["foobar"]; // 2
a.foobar; // 2
```

这里有个问题需要特别注意，如果字符串键值能够被强制类型转换为十进制数字的话，它就会被当作数字索引来处理。

```js
var a = [];
a["13"] = 42;
a.length; // 14
```

#### 字符串

`JavaScript`中字符串是不可变的，而数组是可变的。

字符串不可变是指字符串的成员函数不会改变其原始值，而是创建并返回一个新的字符串。而数组的成员函数都是在其原始值上进行操作。

```js
c = a.toUpperCase();
a === c; // false
a; // "foo"
c; // "FOO"

b.push("!");
b; // ["f","O","o","!"]
```

许多数组函数用来处理字符串很方便。虽然字符串没有这些函数，但可以通过“借用”数组的非变更方法来处理字符串:

```js
a.join; // undefined
a.map; // undefined
var c = Array.prototype.join.call(a, "-");
var d = Array.prototype.map
  .call(a, function(v) {
    return v.toUpperCase() + ".";
  })
  .join("");
c; // "f-o-o"
d; // "F.O.O."
```

#### 数字

大部分现代编程语言中的数字类型都是基于`IEEE 754`标准来实现的，该标准通常被称为"浮点数"。`JavaScript`使用的是"双精度"格式(即 64 位二进制)。

`JavaScript`中的数字常量一般用十进制表示。例如:

```js
var a = 42;
var b = 42.3;
```

数字前面的`0`可以省略:

```js
var a = 0.42;
var b = 0.42;
```

小数点后小数部分最后面的`0`也可以省略:

```js
var a = 42.0;
var b = 42;
```

> `42.`这种写法没问题，只是不常见，但从代码的可读性考虑，不建议这样写。

特别大和特别小的数字默认用指数格式显示，与`toExponential()`函数的输出结果相同。

```js
var a = 5e10;
a; // 50000000000
a.toExponential(); // "5e+10"
var b = a * a;
b; // 2.5e+21
var c = 1 / a;
c; // 2e-11
```

`tofixed(..)`方法可指定小数部分的显示位数,`toPrecision(..)`方法用来指定有效数字的位数，他们返回的都是字符串。

```js
var a = 42.59;
a.toFixed(0); // "43"
a.toFixed(1); // "42.6"
a.toFixed(2); // "42.59"
a.toFixed(3); // "42.590"
a.toFixed(4); // "42.5900"

a.toPrecision(1); // "4e+1"
a.toPrecision(2); // "43"
a.toPrecision(3); // "42.6"
a.toPrecision(4); // "42.59"
a.toPrecision(5); // "42.590"
a.toPrecision(6); // "42.5900"
```

> `.`运算符需要特别注意，因为它是一个有效的数字字符，会被优先识别为数字常量的一部分，然后才是对象属性访问运算符。

```js
// 无效语法:
42.toFixed( 3 ); // SyntaxError

// 下面的语法都有效:
(42).toFixed( 3 ); // "42.000"
0.42.toFixed( 3 ); // "0.420"
42..toFixed( 3 ); // "42.000"
```

机器精度(`machine epsilon`)，对于`JavaScript`来说，这个值通常是`2^-52(2.220446049250313e-16)`。

`ES6`开始，该值定义在`Number.EPSILON`中，`ES6`之前版本的`polyfill`:

```js
if (!Number.EPSILON) {
  Number.EPSILON = MATH.pow(2, -52);
}
```

可以用`Number.EPSILON`来比较两个数字是否相等(在指定的误差范围内):

```js
function numberCloseEnoughToEqual(n1, n2) {
  return Math.abs(n1 - n2) < Number.EPSION;
}

var a = 0.1 + 0.2;
var b = 0.3;
numbersCloseEnoughToEqual(a, b); // true
numbersCloseEnoughToEqual(0.0000001, 0.0000002); // false
```

能够被"安全"呈现的最大整数是`2^53 - 1`，即`9007199254740991`，在`ES6`中被定义为`Number.MAX_SAFE_INTEGER`，最小整数是`-9007199254740991`，在`ES6`中被定义为`Number.MIN_SAFE_INTEGER`。

`ES6`中的`Number.isInteger(..)`方法检测一个值是否是整数，`Number.isSafeInteger(..)`方法检测一个值是否是安全的整数。

### 特殊数值

`undefined`和`null`常被用来表示“空的”值或“不是值”的值。二者之间有一些细微的差别。例如:

- null 指空值(empty value)
- undefined 指没有值(missing value)

或者:

- undefined 指从未赋值
- null 指曾赋过值，但是目前没有值

`null`是一个特殊关键字，不是标识符，我们不能将其当作变量来使用和赋值。然而`undefined`却是一个标识符，可以被当作变量来使用和赋值。

#### void 运算符

`void`运算符不改变表达式的结果，只是让表达式不返回值。

```js
var a = 42;
console.log(void a, a); // undefined 42
```

#### NaN

`isNaN(..)`是内建工具函数，来判断一个值是否是`NaN`。`isNaN(..)`有一个严重的缺陷，它的检查方式过于死板，就是“检查参数是否不是`NaN`，也不是数字”。

```js
var a = 2 / "foo";
var b = "foo";
a; // NaN
b;
("foo");
window.isNaN(a); // true
window.isNaN(b); // true——晕!
```

很明显`"foo"`不是一个数字，但是它也不是`NaN`。这个`bug`自`JavaScript`问世以来就一直存在，至今已超过`19`年。

`ES6`提供了工具函数`Number.isNaN(..)`。之前的`polyfill`如下:

```js
if (!Number.isNaN) {
  Number.isNaN = function(n) {
    return typeof n === "number" && window.isNaN(n);
  };
}

var a = 2 / "foo";
var b = "foo";
Number.isNaN(a); // true
Number.isNaN(b); // false——好!
```

还有一个更简单的方法，利用`NaN`不等于自身这个特点。`NaN`是`JavaScript`中唯一一个不等于自身的值。

```js
if (!Number.isNaN) {
  Number.isNaN = function(n) {
    return n !== n;
  };
}
```

#### 无穷数

```js
var a = 1 / 0; // Infinity即(Number.POSITIVE_INFINITY)
var b = -1 / 0; // -Infinity即(Number.NEGATIVE_INFINITY)
```

计算结果一旦溢出为无穷数(infinity)就无法再得到有穷数。

#### 零值

有些应用程序中的数据需要以级数形式来表示(比如动画帧的移动速度)，数字的符号位(sign)用来代表其他信息(比如移动的方向)。此时如果一个值为`0`的变量失去了它的符号位，它的方向信息就会丢失。所以保留`0`值的符号位可以防止这类情况发生。

#### `Object.is(..)`

`ES6`引入一个工具方法`Object.is(..)`来判断楞个值是否绝对相等，可以用来处理上述所有特殊情况。

```js
var a = 2 / "foo";
var b = -3 * 0;

Object.is(a, NaN); // true
Object.is(b, -0); // true

Object.is(b, 0); // false
```

### 值和引用

`JavaScript`中没有指针，引用的工作机制也不尽相同。在`JavaScript`中变量不可能成为指向另一个变量的引用。

`JavaScript`引用指向的是值。如果一个值有`10`个引用，这些引用指向的都是同一个值，它们相互之间没有引用/指向关系。

`JavaScript`对值和引用的赋值/传递在语法上没有区别，完全根据值的类型来决定。

```js
var a = 2;
var b = a; // b是a的值的一个副本 b++;
a; // 2
b; // 3

var c = [1, 2, 3];
var d = c; // d是[1,2,3]的一个引用 d.push( 4 );
c; // [1,2,3,4]
d; // [1,2,3,4]
```

简单值(即标量基本类型值，`scalar primitive`)总是通过值复制的方式来赋值/传递，包括`null`、`undefined`、字符串、数字、布尔和`ES6`中的`symbol`。

复合值(compound value)——对象(包括数组和封装对象)和函数，则总是通过引用复制的方式来赋值/传递。

上例中`2`是一个标量基本类型值，所以变量`a`持有该值的一个副本，`b`持有它的另一个副本。`b`更改时，`a`的值保持不变。

> ECMAScript 中所有函数的参数都是按值传递的。

```js
function foo(x) {
  x.push(4);
  x; // [1,2,3,4]

  // 然后
  x = [4, 5, 6];
  x.push(7);
  x; // [4,5,6,7]
}

var a = [1, 2, 3];

foo(a);

a; // 是[1,2,3,4]，不是[4,5,6,7]
```

我们向函数传递`a`的时候，实际是将引用`a`的一个副本赋值给`x`，而`a`仍然指向[1,2,3]。

> 我们无法自行决定使用值复制还是引用复制，一切由值的类型来决定。

如果通过值复制的方式来传递复合值(如数组)，就需要为其创建一个副本，这样传递的就不再是原始值。例如:

```js
foo(a.slice());
```

`slice(..)`不带参数会返回当前数组的一个浅副本`(shallow copy)`。由于传递给函数的是指向该副本的引用，所以`foo(..)`中的操作不会影响`a`指向的数组。相反，如果要将标量基本类型值传递到函数内并进行更改，就需要将该值封装到一个复合值(对象、数组等)中，然后通过引用复制的方式传递。

```js
function foo(wrapper) {
  wrapper.a = 42;
}
var obj = {
  a: 2
};

foo(obj);

obj.a; // 42
```

### 小结

简单标量基本类型值(字符串和数字等)通过值复制来赋值/传递，而复合值(对象等)通过引用复制来赋值/传递。`JavaScript`中的引用和其他语言中的引用/指针不同，它们不能指向别的变量/引用，只能指向值。

### 原生函数

`JavaScript`内建函数(`built-in function`)，也叫原生函数(`native function`)，如`String`和`Number`。

常用的原生函数有:

- `String()`
- `Number()`
- `Boolean()`
- `Array()`
- `Object()`
- `Function()`
- `RegExp()`
- `Date()`
- `Error()`
- `Symbol()`---ES6 中新加入!

原生函数可以被当做构造函数来使用，但其构造出来的对象可能会和我们设想的有所出入:

```js
var a = new String("abc");

typeof a; // 是"object",不是"String"

a instanceof String; // true

Object.prototype.toString.call(a); //"[object String]"
```

#### 内部属性`[[Class]]`

所有`typeof`返回值为`"object"`的对象(如数组)都包含一个内部属性`[[Class]]`(我们可以把它看作一个内部的分类，而非传统的面向对象意义上的类)。这个属性无法直接访问，一般通过`Object.prototype.toString(..)`来查看。

```js
Object.prototype.toString.call(null);
// "[object Null]"

Object.prototype.toString.call(undefined);
// "[object Undefined]"

Object.prototype.toString.call("abc");
// "[object String]"

Object.prototype.toString.call(42);
// "[object Number]"

Object.prototype.toString.call(true);
// "[object Boolean]"

Object.prototype.toString.call({name: "hehe"});
// "[object Object]"

Object.prototype.toString.call([1, 2, 3]);
// "[object Array]"

Object.prototype.toString.call(/regex-literal/i);
// "[object RegExp]"

Object.prototype.toString.call(function() {});
// "[object Function]"
```

### 封装对象包装

封装对象(object wrapper)扮演着十分重要的角色。由于基本类型值没有`.length`和`.toString()`这样的属性和方法，需要通过封装对象才能访问，此时`JavaScript`会自动为基本类型值包装(box 或者 wrap)一个封装对象:

```js
var a = "abc";

a.length; // 3
a.toUpperCase(); // "ABC"
```

### 拆封

想要得到拆封对象中的基本类型值，可以使用`valueOf()`函数:

```js
var a = new String("abc");
var b = new Number(42);
var c = new Boolean(true);

a.valueOf(); // "abc"
b.valueOf(); // 42
c.valueOf(); // true
```

### 将原生函数作为构造函数

应该尽量避免使用构造函数。

> 将包含至少一个"空单元"的数组称为"稀疏数组"。

```js
var a = Array.apply(null, { length: 3 });
a; // [ undefined, undefined, undefined ]
```

`apply`第二个参数则必须是一个数组(或者类似数组的值，也叫作类数组对象，array-like object)，其中的值被用作函数的参数。

我们执行的实际上是`Array(undefined, undefined, undefined)`。

`ES5`引入一个简单的获取当前时间的方法`Date.now()`。对`ES5`之前的版本可以使用`polyfill`:

```js
if (!Date.now) {
  Date.now = function() {
    return new Date().getTime();
  };
}
```

`Symbol`比较特殊，不能带`new`关键字，否则会报错。

> 符号并非对象，而是一种简单标量基本类型。

### 强制类型转换

将值从一种类型转换为另一种类型通常称为类型转换(`type casting`)，这是显式的情况；隐式的情况称为强制类型转换(`coercion`)。

`JS`中的强制类型转换总是返回标量基本类型值，如字符串、数字和布尔值。

类型转换发生在静态类型语言的编译阶段，而强制类型转换则发生在动态类型语言的运行时(runtime)。

或者用"隐式强制类型转换"(`implicit coercion`)和"显式强制类型转换"(`explicit coercion`)来区分。

```js
var a = 42;
var b = a + ""; // 隐式强制类型转换
var c = String(a); // 显式强制类型转换
```

#### 抽象值操作

##### toString

基本类型值的转换规则:

- `null` --> `"null"`
- `undefined` --> `"undefined"`
- `true` --> `"true"`

对于普通对象来说，除非自行定义，否则`toString()`或者`Object.prototype.toString()`返回内部属性`[[Class]]`的值，如`"[object Object]"`。

##### JSON 字符串转化

大多数简单值，`JSON`字符串化和`toString()`效果基本相同。

```js
JSON.stringify(42); // "42"
JSON.stringify("42"); // ""42""(含有双引号的字符串)
JSON.stringify(null); // "null"
JSON.stringify(true); // "true"
```

所有安全的`JSON`值(`JSON-safe`)都可以使用`JSON.stringify(..)`字符串化。安全的`JSON`值是指能够呈现为有效`JSON`格式的值。

为了简单起见，我们来看看什么是不安全的`JSON`值。`undefined`、`function`、`symbol`(ES6+)和包含循环引用(对象之间相互引用，形成一个无限循环)的对象都不符合`JSON`结构标准，支持`JSON`的语言无法处理它们。

`JSON.stringify(..)`在对象中遇到`undefined`、`function`和`symbol`时会自动将其忽略，在数组中则会返回`null`(以保证单元位置不变)。

```js
JSON.stringify(undefined); // undefined
JSON.stringify(function() {}); // undefined
JSON.stringify([1, undefined, function() {}, 4]); // "[1,null,null,4]"
JSON.stringify({ a: 2, b: function() {} }); // "{"a":2}"
```

如果对象中定义了`toJSON()`方法，`JSON`字符串化时会首先调用该方法，然后用它的返回值来进行序列化。

```js
var o = {};

var a = {
  b: 42,
  c: o,
  d: function() {}
};

// 在a中创建一个循环引用
o.e = a;

// 循环引用在这里会产生错误
// JSON.stringify( a );

// 自定义的JSON序列化
a.toJSON = function() {
  // 序列化仅包含b
  return { b: this.b };
};

JSON.stringify(a); // "{"b":42}"
```

`toJSON()`应该"返回一个能够被字符串化的安全的`JSON`值"，而不是"返回一个`JSON`字符串"。

还可以向`JSON.stringify`传递一个可选参数`replacer`，可以是数组或者函数，用来指定对象序列化过程中哪些属性应该被处理，哪些应该被排除。

如果`replacer`是一个数组，那么它必须是一个字符串数组，其中包含序列化要处理的对象的属性名称，除此之外其他的属性则被忽略。

如果`replacer`是一个函数，它会对对象本身调用一次，然后对对象中的每个属性各调用一次，每次传递两个参数，键和值。如果要忽略某个键就返回`undefined`，否则返回指定的值。

```js
var a = {
  b: 42,
  c: "42",
  d: [1, 2, 3]
};
JSON.stringify(a, ["b", "c"]); // "{"b":42,"c":"42"}"
JSON.stringify(a, function(k, v) {
  if (k !== "c") return v;
});
// "{"b":42,"d":[1,2,3]}"
```

`JSON.string`还有一个可选参数`space`，用来指定输出的缩进格式。`space`为正整数时是指定每一级缩进的字符数，它还可以是字符串，此时最前面的十个字符被用于每一级的缩进。

```js
JSON.stringify(a, null, 3);
JSON.stringify(a, null, "-----");
```

#### toNumber

- `true` --> `1`
- `false` --> `0`
- `undefined` --> `NaN`
- `null` --> `0`

对象(包括数组)会首先被转换为相应的基本类型值，如果返回的是非数字的基本类型值，则在遵循以上规则将其转换为数字。

为了将值转换为相应的基本类型值，抽象操作`ToPrimitive`会首先(通过内部操作`DefaultValue`)检查该值是否有`valueOf()`方法。如果有并且返回基本类型值，就使用该值进行强制类型转换。如果没有就使用`toString()`的返回值(如果存在)来进行强制类型转换。

如果`valueOf()`和`toString()`均不返回基本类型值，会产生`TypeError`错误。

```js
var a = {
  valueOf: function() {
    return "42";
  }
};

var b = {
  toString: function() {
    return "42";
  }
};

var c = [4, 2];
c.toString = function() {
  return this.join(""); // "42"
};

Number(a); // 42
Number(b); // 42
Number(c); // 42
Number(""); // 0
Number([]); // 0
Number(["abc"]); // NaN
```

#### toBoolean

##### falsy value

- `undefined`
- `null`
- `false`
- +0、-0 和 NaN
- ""

除了`falsy value`其余都是`truthy value`

```js
var a = new Boolean(false);
var b = new Number(0);
var a = new String("");

var d = Boolean(a && b && c);
d; // true
```

#### 显式强制类型转换

```js
var c = "3.14";
var d = +c;
d; // 3.14
```

一元`+`运算符显式将`c`转换为数字。

`JavaScript`中字符串的`indexOf(..)`方法也遵循这一惯例，该方法在字符串中搜索指定的子字符串，如果找到就返回子字符串所在的位置(从`0`开始)，否则返回`-1`。

```js
var a = "Hello World";
if (a.indexOf("lo") >= 0) {
  // true
  // 找到匹配!
}
if (a.indexOf("lo") != -1) {
  // true
  // 找到匹配!
}
if (a.indexOf("ol") < 0) {
  // true
  // 没有找到匹配!
}
if (a.indexOf("ol") == -1) {
  // true
  // 没有找到匹配!
}
```

`>= 0`和`== -1`这样的写法不是很好，称为“抽象渗漏”，意思是在代码中暴露了底层的实现细节，这里是指用`-1`作为失败时的返回值，这些细节应该被屏蔽掉。

如果`indexOf(..)`返回`-1`，`~`将其转换为假值`0`，其他情况一律转换为真值。

```js
var a = "Hello World";

~a.indexOf("lo"); // -4 <-- 真值!

if (~a.indexOf("lo")) {
  // true
  // 找到匹配!
}

~a.indexOf("ol"); // 0 <-- 假值!
!~a.indexOf("ol"); // true

if (!~a.indexOf("ol")) {
  // true
  // 没有找到匹配!
}
```

> 建议使用`Boolean(a)`和`!!a`来显式转换以便让代码更加清晰易读。

### 隐式强制类型转换

```js
var a = [1, 2];
var b = [3, 4];
a + b; // "1,23,4"
```

如果某个操作数是字符串或者能够通过以下步骤转换为字符串的话，`+`将进行拼接操作。如果其中一个操作数是对象(包括数组)，则首先对其调用`ToPrimitive`抽象操作(规范 9.1 节)，该抽象操作再调用`[[DefaultValue]]`(规范 8.12.8 节)，以数字作为上下文。

简单来说就是，如果`+`的其中一个操作数是字符串(或者通过以上步骤可以得到字符串)，则执行字符串拼接;否则执行数字加法。

```js
var a = {
  valueOf: function() {
    return 42;
  },
  toString: function() {
    return 4;
  }
};
a + ""; // "42"
String(a); // "4"
```

下面的情况会发生 布尔值隐式强制类型转换。

1. if (..) 语句中的条件判断表达式。
1. for ( .. ; .. ; .. ) 语句中的条件判断表达式(第二个)。
1. while (..) 和 do..while(..) 循环中的条件判断表达式。
1. ? : 中的条件判断表达式。
1. 逻辑运算符 ||(逻辑或)和 &&(逻辑与)左边的操作数(作为条件判断表达式)。

```js
a || b;
// 大致相当于(roughly equivalent to):
a ? a : b;

a && b;
// 大致相当于(roughly equivalent to):
a ? b : a;
```

#### 宽松相等和严格相等

宽松相等`==`(`loose equals`)，严格相等`===`(`strict equals`)

几个非常规的情况:

- `NaN`不等于`NaN`
- `+0`等于`-0`

对象(包括数组和函数)的宽松相等`==`，两个对象指向同一个值即视为相等，不发生强制类型转换。比较对象的时候，`==`和`===`的工作原理相同。

##### 字符串和数字之间比较

```js
var a = 42;
var b = "42";

a === b; // false
a == b; // true
```

规范规定:

1. 如果`Type(x)`是数字，`Type(y)`是字符串，则返回`x == ToNumber(y)`的结果;
1. 如果`Type(x)`是字符串，`Type(y)`是数字，则返回`ToNumber(x) == y`的结果。

字符串和数字比较将字符串转化为数字再进行比较。

##### 其他类型和布尔类型之间的相等比较

```js
var a = "42";
var b = true;

a == b; // false
```

规范规定:

1. 如果`Type(x)`是布尔类型，则返回`ToNumber(x) == y`的结果;
1. 如果`Type(y)`是布尔类型，则返回`x == ToNumber(y)`的结果。

即先把`b`转化为`1`,得到`'42' == 1`。

##### `null`和`undefined`之间的相等比较

在`==`中`null`和`undefined`相等。

条件判断`a == null`仅在`a`值为`null`或`undefined`时成立。

##### 对象和非对象之间的相等比较

规范规定:

1. 如果`Type(x)`是字符串或数字，`Type(y)`是对象，则返回`x == ToPrimitive(y)`的结果;
1. 如果`Type(x)`是对象，`Type(y)`是字符串或数字，则返回`ToPromitive(x) == y`的结果。

```js
var a = 42;
var b = [42];

a == b; // true
```

`[42]`首先调用`ToPromitive`抽象操作，返回`"42"`，变成`"42" == 42`，然后又变成`42 == 42`，最后两者相等。

"打开"封装对象(如`new String("abc")`)，返回其中的基本数据类型值(`"abc"`)。`==`中的`ToPromitive`强制类型转换也会发生这样的情况:

```js
var a = "abc";
var b = Object(a); // 和new String(a)一样

a === b; // false
a == b; // true
```

`Object`构造函数为给定值创建一个对象包装器。如果给定值是`null`或`undefined`，将会创建并返回一个空对象，否则，将返回一个与给定值对应类型的对象。

```js
var a = null;
var b = Object(a);
a == b; // false

var a = undefined;
var b = Object(a);
a == b; // false
```

#### 比较少见的情况

```js
Number.prototype.valueOf = function() {
  return 3;
};

new Number(2) == 3; // true
```

##### 假值的相等比较

```js
"0" == null; // false
"0" == undefined; // false
"0" == false; // true
"0" == NaN; // false
"0" == 0; // true
"0" == ""; // false

false == null; // false
false == undefined; // false
false == NaN; // false
false == 0; // true
false == ""; // true
false == []; // true
false == {}; // false

"" == null; // false
"" == undefined; // false
"" == NaN; // false
"" == 0; // true
"" == []; // true
"" == {}; // false

0 == null; // false
0 == undefined; // false
0 == NaN; // false
0 == []; // true
0 == {}; // false
```

```js
[] == ![]; // true
2 == [2]; // true
"" == [null]; // true
0 == "\n"; // true
```

根据`ToBoolean`规则，会对`[]`进行显式强制类型转换(同时反转奇偶位校验位)，变成`[] == false`。

#### 抽象关系比较

前提双方非字符串。

比较双方首先调用`ToPrimitive`，如果结果出现非字符串，就根据`ToNumber`规则将双方强制类型转换为数字来进行比较。

```js
var a = [42];
var b = ["43"];

a < b; // true
b < a; // false
```

如果双方都是字符串，则按字母顺序来进行比较:

```js
var a = ["42"];
var b = ["043"];

a < b; // false
```

```js
var a = { b: 42 };
var b = { b: 43 };
a < b; // false
a == b; // false
a > b; // false
a <= b; // true
a >= b; // true
```

`JavaScript`中对`<=`的处理是"不大于的意思",即`!(a > b)`。

![JS中的相等比较](http://ovyn493ey.bkt.clouddn.com/equals.png)

### 语法

#### 语句和表达式

`JavaScript`如同自然语言一样。语句相当于句子，表达式相当于短语，运算符则相当于标点 符号和连接词。

```js
var a = 3 * 6;
var b = a;
b;
```

第三行代码中只有一个表达式`b`，同时它也是一个语句,这样的情况通常叫做"表达式语句"(expression statement)。

##### 语句的结果值

语句都有一个结果值(statement completion value, undefined 也算)。

规范定义`var`的结果值是`undefined`。在控制台中输入`var a = 42`会得到结果值`undefined`。

`{ .. }`代码块的结果值是其最后一个语句/表达式的结果。

```js
var b;
if (true) {
  b = 4 + 38;
}
```

代码块的返回结果值就如同一个隐式的返回，即返回最后一个语句的结果值。

这样的代码无法运行:

```js
var a, b;

a = if (true) {
    b = 4 + 38;
};
```

语法不允许将获得语句的结果值赋值给另一个变量。

##### 上下文规则

```js
{
  foo: bar();
}
```

这里是`label`语句，`{ .. }`是一个普通的代码块。

```js
[] + {}; // "[object Object]"
{
}
+[]; // 0
```

第一行代码中，`{}`出现在`+`运算符表达式中，会被当做一个空对象来处理。

第二行代码中，`{}`被当作一个独立的空代码块(不执行任何操作)。

##### `else if`和可选代码块

```js
if (a) {
  // ..
} else if (b) {
  // ..
} else {
  // ..
}
```

实际上`JavaScript`没有`else if`，`if`和`else`只包含单条语句的时候可以省略代码块的`{ }`。

```js
if (a) doSomething(a);
```

`else if`实际上是这样的:

```js
if (a) {
  // ..
} else {
  if (b) {
    // ..
  } else {
    // ..
  }
}
```

### 运算符优先级(operator precedence)

`&&` > `||` > `? :`

#### 关联

运算符的关联(associativity)不是从左到右就是从右到左。

`? :`和`=`操作符都是右关联操作符。

## chap4. 异步和性能

### 4.1 异步：现在与将来

程序在现在运行的部分和将来运行的部分之间的关系就是异步编程的核心。

#### 异步控制台

`console.*`方法是由宿主环境添加到`JavaScript`中的。是`I/O`异步操作，并不是同步执行。

```js
var a = {
  index: 1
};

console.log(a);

a.index++;
```

> 最好的选择是在`JavaScript`调试器中使用断点，不要依赖控制台输出，次优化的方案是吧对象序列化到一个字符串中，强制执行一次“快照”，比如通过`JSON.stringify(..)`。

#### 事件循环

循环的每一轮称为一个`tick`。

`setTimeout(..)`只能确保回调函数不会在指定的时间间隔之前运行。

#### 任务队列(job queue)

任务队列是挂在事件循环队列的每个`tick`之后的一个队列。在事件循环的每个`tick`中，可能出现的异步动作不会导致一个完整的新事件添加到事件循环队列中，而会在当前`tick`的任务队列尾添加一个项目(一个任务)。

#### 单线程

异步回调执行可能有先后，这是`JavaScript`的一个不确定性。

在`JavaScript`的特性中，这种函数顺序的不确定性就是通常所说的竞态条件(`race condition`)，`foo()`和`bar()`相互竞争，看谁先运行。具体来说，因为无法可靠预测`a`和`b`的最终结果，所以才是竞态条件。

```js
var a, b;
function foo(x) {
  a = x * 2;
  if (a && b) {
    baz();
  }
}

function bar(y) {
  b = y * 2;
  if (a && b) {
    baz();
  }
}

function baz() {
  console.log(a + b);
}

// ajax(..)是某个库中的某个Ajax函数
ajax("http://some.url.1", foo);
ajax("http://some.url.2", bar);
```

#### 小结

一旦有事件需要运行，事件循环就会运行，知道队列清空。事件循环的每一轮称为一个`tick`。用户交互、IO 和定时器会向事件队列中加入事件。

两个或多个"进程"在同一程序内并发地交替运行他们的步骤/事件时，如果这些任务彼此不想管，就不一定需要交互，如果进程间没有相互影响的话，不确定性完全可以接受。如果出现了交互，就需要对他们的交互进行协调。

### 4.2 回调

#### 延续(continuation)

```js
// A
ajax( "..", function(..) {
    // C
});
// B
```

回调函数包裹或者说封装了程序的延续(continuation)。

#### 嵌套回调与链式回调

```js
listen("click", function handler(evt) {
  setTimeOut(function request() {
    ajax("http://some.url.1", function response(text) {
      if (text == "hello") {
        handler();
      } else if (text == "world") {
        request();
      }
    });
  }, 500);
});
```

这种代码常常被称作回调地狱(callback hell)，有时也被称为毁灭金字塔(pyramid of doom，得名于嵌套缩进产生的横向三角形状)。

```js
listen("click", handle);

function handler() {
  setTimeOut(request, 500);
}

function request() {
  ajax("..", response);
}

function response(text) {
  if (text == "hello") {
    handler();
  } else if (text == "world") {
    request();
  }
}
```

链式代码组织形式在线性(顺序)追踪代码过程中，在整个代码中跳来跳去以查看流程。

手工硬编码回调的真正问题在于一旦指定(也就是预先计划)了所有可能事件和路径，代码就会变的非常复杂，以至于无法维护和更新。嵌套和缩进基本上只是转移注意力的枝节。

"error-first 风格"，有时候也叫"Node 风格"，其中回调的第一个参数保留用作错误对象。如果成功的话，这个参数就会被清空/置假。

#### 小结

1. 我们需要一种更同步、更顺序、更阻塞的方式来表达异步，就像大脑一样。
1. 更重要的一点，回调会收到控制反转的影响，因为回调暗中把控制权交给第三方来调用代码中的`continuation`，这种控制转移带来一系列麻烦的信任问题，比如回调被调用的次数是否会超出预期。

### Promise

回调的两个主要缺陷：

1. 缺乏顺序性
1. 缺乏可信任性

把控制反转回来，不把程序`continuation`传给第三方，而是希望第三方给我们提供了解其任务何时结束的能力，然后由我们自己的代码来决定下一步做什么。

#### 什么是`Promise`

##### 未来值

设想一个场景：麦当劳买汉堡，付款(发出对某个 value(这里是汉堡)的请求)，拿取号单(这代表未来的汉堡)，订单号就是一个 promise，等待期间可以做其他事情，比如玩手机，当通知取餐的时候，凭 value-promise 换取这个 value(汉堡)。

有两种结果，拿到汉堡和汉堡售罄。

当编写代码要得到某个值的时候，比如通过数学计算，不管你有没有意识到，你都已经对这个值做出了一些非常基本的假设，那就是，它已经是一个具体的现在值:

```js
var x,
  y = 2;

console.log(x + y); // NaN 因为x还没有定
```

设想一种方式表达:"把 x 和 y 加起来，
但如果它们中的任何一个还没有准备好，就等待两者都准备好。一旦可以就马上执行加运算。"

用回调函数处理：

```js
function add(getX, getY, cb) {
  var x, y;
  getX(function(xVal) {
    x = xVal;
    // 两个都准备好了?
    if (y != undefined) {
      cb(x + y); // 发送和
    }
  });
  getY(function(yVal) {
    y = yVal;
    // 两个都准备好了?
    if (x != undefined) {
      cb(x + y); // 发送和
    }
  });
}
// fetchX()和fetchY()是同步或者异步函数
add(fetchX, fetchY, function(sum) {
  console.log(sum); // 是不是很容易?
});
```

为了统一处理现在和将来，我们把它们都变成了将来，即所有操作都成了异步的。

##### `Promise`值

先大致看一下如何通过`Promise`函数表达这个`x + y`的例子：

```js
function add(xPromise, yPromise) {
  // Promise.all([..])接受一个promise数组并返回一个新的promise
  // 这个新的promise等待数组中的所有promise完成
  return (
    Promise.all([xPromise, yPromise])

      // 这个promise决议之后，我们取得收到的X和Y值并加在一起
      .then(function(values) {
        // values是来自于之前决议的promise的消息数组
        return values[0] + values[1];
      })
  );
}

// fetchX()和fetchY()返回相应值得promise，可能已经就绪
// 也可能以后就绪
add(fetchX(), fetchY())
  // 我们得到一个这两个数组的和的promise
  // 现在链式调用then(..)来等待返回promise的决议
  .then(function(sum) {
    console.log(sum); // 这更简单！
  });
```

使用事件侦听对象对`Promise`进行模拟。

```js
function foo(x) {
  // 开始做点可能耗时的工作
  // 构造一个listener事件通知处理对象来返回

  return listener;
}

var evt = foo(42);

evt.on("conpletion", function() {
  // 可以进行下一步！
});

evt.on("faliure", function() {
  // 啊，foo(..)中出错了
});
```

回调本身表达了一种控制反转，对回调模式的反转实际上是对反转的反转。

#### 具有`then`方法的鸭子类型

如何去判断一个值是否是真正的`Promise`

鸭子类型(duck typing)--“如果它看起来像只鸭子，叫起来像只鸭子，那它就一定是个鸭子”。

对一个有`then(..)`函数的对象完成一个`Promise`，它们会自动被识别为`thenable`，并按特定的规则处理。

#### `Promise`信任问题

1.调用过早

对一个`Promise`调用`then(..)`的时候，即使这个`Promise`已经决议，提供给`then(..)`的回调也总是会被异步调用。

2.调用过晚

当`Promise`创建对象调用`resolve(..)`或`reject(..)`时，这个`Promise`的`then(..)`注册的观察回调就会被自动调度。

3.回调未调用

没有任何东西能够阻止`Promise`向你通知它的决议(如果它没有决议的话)。如果你对一个`Promise`注册了一个完成回调和一个拒绝回调，那么`Promise`在决议时总会调用其中的一个。

如果`Promise`本身永远不被决议，`Promise`也提供了解决方案，使用了竞态的高级抽象机制：

```js
// 用于超时一个Promise的工具
function timeoutPromise(delay) {
  return new Promise(function(resolve, reject) {
    setTimeout(function() {
      reject("Timeout!");
    }, delay);
  });
}

// 设置foo()超时
Promise.race([
  foo(), //试着开始foo()
  timeoutPromise(3000) //给它3秒钟
]).then(
  function() {
    // foo(..)及时完成！
  },
  function(err) {
    // 或者foo()被拒绝，或者只是没能按时完成
    // 查看err来了解是那种情况
  }
);
```

##### 调用次数过少或过多

`Promise`的定义方式使得他只能够被决议一次。由于`Promise`只能被决议一次，所以任何通过`then(..)`注册的(每个)回调就只会被调用一次。

##### 吞掉错误或异常

如果拒绝一个`Promise`并给出一个理由(也就是
一个出错消息)，这个值就会被传给拒绝回调。

如果在`Promise`的创建过程中或在查看其决议结果过程中的任何时间点上出现了一个`JavaScript`异常错误，比如一个`TypeError`或`ReferenceError`，那这个异常就会被捕捉，并且会使这个`Promise`被拒绝。

```js
var p = new Promise(function(resolve, reject) {
  foo.bar(); // foo未定义，所以会出错!
  resolve(42); // 永远不会到达这里 :(
});

p.then(
  function fulfilled() {
    // 永远不会到达这里 :(
  },
  function rejected(err) {
    // err将会是一个TypeError异常对象来自foo.bar()这一行
  }
);
```

#### `Promise.resolve(..)`

`Promise`没有完全摆脱回调，只是改变传递回调的位置，我们并不是把回调函数传给`foo(..)`，而是从`foo(..)`得到某个东西，然后把回调传给这个东西。

如果向`Promise.resolve(..)`传递一个非`Promise`、非`thenable`的立即值，就会得到一个用这个值填充的`Promise`。

```js
var p1 = new Promise(function(resolve, reject) {
  resolve(42);
});

var p2 = Promise.resolve(42);
```

如果向`Promise.resolve(..)`中传入一个真正的`Promise`，那么得到的就是它本身。

```js
var p1 = Promise.resolve(42);
var p2 = Promise.resolve(p1);
p1 === p1; // true
```

对于用`Promise.resolve(..)`为所有的函数返回值都封装一层，这样做很容易把函数调用规范定义为良好的异步任务，它能够保证总返回一个`Promise`结果。

#### 链式流

`Promise`两个固有特性

- 每次你对`Promise`调用`then(..)`，它都会创建并返回一个新的`Promise`，我们可以将其链接起来;
- 不管从`then(..)`调用的完成回调(第一个参数)返回的值是什么，它都会被自动设置为被链接`Promise`(第一点中的)的完成。

调用`then(..)`时的完成处理函数或拒绝处理函数如果抛出异常，都会导致(链中的)下一个`promise`因这个异常而立即被拒绝。

- 调用`Promise的then(..)`会自动创建一个新的`Promise`从调用返回。
- 在完成或拒绝处理函数内部，如果返回一个值或抛出一个异常，新返回的(可链接的)`Promise`就相应地决议。
- 如果完成或拒绝处理函数返回一个`Promise`，它将会被展开，这样一来，不管它的决议值是什么，都会成为当前`then(..)`返回的链接`Promise`的决议值。

#### 术语: 决议、完成以及拒绝

- 决议(resolve)
- 完成(fulfill)
- 拒绝(reject)

第二个参数名很容易定，几乎所有文献都将其命名为`reject(..)`。

`Promise.resolve(..)`会将传入的真正`Promise`直接返回，对传入的`thenable`则会展开。如果这个`thenable`展开得到一个拒绝状态，那么从`Promise. resolve(..)`返回的`Promise`实际上就是这同一个拒绝状态。

`Promise(..)`构造器的第一个参数回调会展开`thenable`(和`Promise.resolve(..)`一样)或真正的`Promise`:

```js
var rejectedPr = new Promise(function(resolve, reject) {
  // 用一个被拒绝的promise完成这个promise
  resolve(Promise.reject("Oops"));
});
rejectedPr.then(
  function fulfilled() {
    // 永远不会到达这里
  },
  function rejected(err) {
    console.log(err); // "Oops"
  }
);
```

`then(..)`的回调，ES6 规范将这两个回调命名为`onFulfilled(..)`和`onRjected(..)`，所以这两个术语很准确。

#### 错误处理

错误处理最自然的方式就是同步`try..catch`结构，它只能同步，无法用于异步代码模式:

`Promise`中的错误处理，其中拒绝处理函数被传递给`then(..)`。`Promise`没有采用流行的`error-first`回调设计风格，而是使用了分离回调(`split-callback`)风格。一个回调用于完成情况，一个回调用于拒绝情况:

```js
var p = Promise.reject("Oops");
p.then(
  function fulfilled() {
    // 永远不会到达这里
  },
  function rejected(err) {
    console.log(err); // "Oops"
  }
);
```

表面来看，这种出错模式很合理。

```js
var p = Promise.resolve(42);
p.then(
  function fulfilled(msg) {
    // 数字没有string函数，所以会抛出错误
    console.log(msg.toLowerCase());
  },
  function rejected(err) {
    // 永远不会到达这里
  }
);
```

如果`msg.toLowerCase()`合法地抛出一个错误(事实确实如此!)，为什么我们的错误处理函数没有得到通知呢?正如前面解释过的，这是因为那个错误处理函数是为`promise p`准备的，而这个`promise`已经用值`42`填充了。`promise p`是不可变的，所以唯一可以被通知这个错误的`promise`是从`p.then(..)`返回的那一个，但我们在此例中没有捕捉。

```js
var p = Promise.resolve(42);
p.then(function fulfilled(msg) {
  // 数字没有string函数，所以会抛出错误
  console.log(msg.toLowerCase());
}).catch(handleErrors);
```

问题似乎解决了，但是如果`handlErrors`内部也有错误就很头疼，后面问题太高深，先不展开讨论。

#### Promise 模式

1.`Promise.all([..])`

从`Promise.all([ .. ])`返回的主`promise`在且仅在所有的成员`promise`都完成后才会完成。如果这些`promise`中有任何一个被拒绝的话，主`Promise.all([..])promise`就会立即被拒绝，并丢弃来自其他所有`promise`的全部结果。

2.`Promise.race([..])`

与`Promise.all([..])`类似，一旦有任何一个`Promise`决议为完成，`Promise.race([..])`就会完成;一旦有任何一个`Promise`决议为拒绝，它就会拒绝。

3.语义的变体

3.1 `none([ .. ])`

这个模式类似于`all([ .. ])`，不过完成和拒绝的情况互换了。所有的`Promise`都要被拒绝，即拒绝转化为完成值，反之亦然。

3.2 `any([ .. ])`

这个模式与`all([ .. ])`类似，但是会忽略拒绝，所以只需要完成一个而不是全部。

3.3 `first([ .. ])`

这个模式类似于与`any([ .. ])`的竞争，即只要第一个`Promise`完成，它就会忽略后续的任何拒绝和完成。

3.4 `last([ .. ])`

这个模式类似于`first([ .. ])`，但却是只有最后一个完成胜出。

`polyfill`一个`first([ .. ])`

```js
// polyfill安全的guard检查
if (!Promise.first) {
  Promise.first = function(prs) {
    return new Promise(function(resolve, reject) {
      // 在所有promise上循环
      prs.forEach(function(pr) {
        // 把值规整化
        Promise.resolve(pr)
          // 不管哪个最先完成，就决议主promise
          .then(resolve);
      });
    });
  };
}
```

#### Promise API 概述

1.`new Promise(..)`构造器

`Promise(..)`必须和`new`一起使用，并且必须提供一个函数回调。这个回调是同步的(立即调用)。这个函数接受两个函数回调，用以支持`promise`决议。通常把这两个函数称为`resolve(..)`和`reject(..)`：

```js
var p = new Promise(function(resolve, reject) {
  // resolve(..)用以决议/完成这个promise
  // reject(..)用于拒绝这个promise
});
```

如果传给`resolve(..)`的是一个真正的`Promise`或`thenable`值，这个值就会被递归展开，并且(要构造的)`promise`将取用其最终决议值或状态。

2.`Promise.resolve(..)`和`Promise.reject(..)`

创建一个已被拒绝的`Promise`的快捷方式是使用`Promise.reject(..)`，所以以下两个
`Promise`是等价的:

```js
var p1 = new Promise(function(resolve, reject) {
  reject("Oops");
});

var p2 = Promise.reject("Oops");
```

3.`then(..)`和`catch(..)`

每个`Promise`实例都有`then(..)`和`catch(..)`方法，通过这两个方法为这个`Promise`注册完成和拒绝回调函数。`Promise`决议后，立即会调用这两个处理函数之一，而且是异步调用。

`then(..)`接受一个或两个参数:第一个用于完成回调，第二个用于拒绝回调。如果两者中的任何一个被省略或者作为非函数值传入的话，就会替换为相应的默认回调。默认完成回调只是把消息传递下去，而默认拒绝回调则只是重新抛出(传播)其接收到的出错原因。

`catch(..)`只接受一个拒绝回调，等价于`then(null,..)`。

`then(..)`和`catch(..)`会创建并返回一个新的`Promise`，这个`Promise`可以用于实现`Promise`链式流程控制。

4.`Promise.all([ .. ])`和`Promise.race([ .. ])`

对`Promise.all([ .. ])`来说，只有传入的所有`Promise`都完成，返回`Promise`才能完成。如果有任何`Promise`被拒绝，返回的主`Promise`就立即会被拒绝(抛弃任何其他`Promise`的结果)。如果完成的话，你会得到一个数组，其中包含传入的所有`Promise`的完成值。对于拒绝的情况，你只会得到第一个拒绝`Promise`的拒绝理由值。这种模式传统上被称为门: 所有人都到齐了才开门。

对`Promise.race([ .. ])`来说，只有第一个决议的`Promise`(完成或拒绝)取胜，并且其决议结果成为返回`Promise`的决议。这种模式传统上称为门闩:第一个到达者打开门闩通过。

#### `Promise`局限性

#### 小结

`Promise`解决了因只用回调的代码而备受困扰的控制反转问题。

它并没有摒弃回调，只是把回调的安排转交给了一个位于我们和其他工具之间的可信任的中介机制。

### 生成器
