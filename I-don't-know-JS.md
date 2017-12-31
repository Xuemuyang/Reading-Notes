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
将AST转化为一组机器指令

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
1. 函数声明
1. 变量声明

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

## `this`和对象原型

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

+ `call`
+ `apply`
+ `bind`

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

#### ES6箭头函数

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
1. `number`
1. `boolean`
1. `null`
1. `undefined`
1. `object`

`null`会被当做对象是语言本身的一个bug,`typeof null`会返回字符串`"object"`。

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

#### 3.3.4 复制对象

##### 深复制和浅复制

> 浅复制是对对象地址的复制，并没有开辟新的栈，也就是复制的结果是两个对象指向同一个地址，修改其中一个对象的属性，则另一个对象的属性也会改变，而深复制则是开辟新的栈，两个对象对应两个不同的地址，修改一个对象的属性，不会改变另一个对象的属性。

对于JSON安全的对象来说，有一种巧妙的复制方法：

```js
var newObj = JSON.parse( JSON.stringify( someObj ) );
```

#### 3.3.5 属性描述符

ES5开始，所有的属性都具备了属性描述符

```js
var myObject = {
    a:2
};

Object.getOwnPropertyDescriptor( myObject, "a" );
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

Object.defineProperty( myObject, "a", {
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

Object.defineProperty( myObject, "FAVORITE_NUMBER", {
    value: 42,
    writeble: false,
    configurable: false
} );
```

2.禁止扩展

禁止一个对象添加新属性并且保留已有属性，可以使用`Object.preventExtensions(..)`:

```js
var myObject = {
    a:2
};

Object.preventExtensions( myObject );

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

1. 属性是否是访问描述符(参见3.3.9节)?如果是并且存在`setter`就调用`setter`。
1. 属性的数据描述符中`writable`是否是`false`?如果是，在非严格模式下静默失败，在严格模式下抛出`TypeError`异常。
1. 如果都不是，将该值设置为属性的值。

如果对象中不存在这个属性，[[Put]]操作更加复杂

#### 3.3.9 Getter和Setter

当给一个属性定义`getter`、`setter`或者两者都有时，这个属性会被定义为“访问描述符”（和“数据描述符”相对）。对于访问描述符来说，JS会忽略他们的`value`和`writeble`特性，取而代之的是关心`set`和`get`（还有`configurable`和`enumerable`）特性。

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
    a:2
};

("a" in myObject); // true
("b" in myObject); // false

myObject.hasOwnProperty( "a" ); // true
myObject.hasOwnProperty( "b" ); // false
```

`in`操作符会检查属性是否在对象及其[[Prototype]]原型链中，`hasOwnProperty(..)`只会检查属性是否在`myObject`对象中，不会检查[[Prototype]]链。

> 在数组上应用`for ..in`循环有时会产生出人意料的结果，最好只在对象上应用`for ..in`循环，如果要遍历数组就使用传统的`for`循环来遍历数组索引。

```js
var myObject = { };

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

myObject.propertyIsEnumerable( "a" ); // true
myObject.propertyIsEnumerable( "b" ); // false

Object.keys( myObject ); // ["a"]
Object.getOwnPropertyNames( myObject ); // ["a", "b"]
```

`propertyIsEnumerable(..)`会检查给定的属性名是否直接存在于对象中(而不是在原型链上)并且满足`enumerable:true`。

`Object.keys(..)`会返回一个数组，包含所有可枚举属性，`Object.getOwnPropertyNames(..)`会返回一个数组，包含所有属性，无论它们是否可枚举。

`in`和`hasOwnProperty(..)`的区别在于是否查找[[Prototype]]链，然而，`Object.keys(..)`和`Object.getOwnPropertyNames(..)`都只会查找对象直接包含的属性。

### 3.4 遍历

> 遍历对象属性时的顺序是不确定的，不同`JavaScript`引擎可能不一样。

ES6增加的`for ..of`循环语法直接遍历数组的值（如果对象本身定义了迭代器的话也可以遍历对象）：

```js
var myArray = [1, 2, 3];

for (var v of myArray) {
    console.log( v );
}
// 1
// 2
// 3
```

JavaScript 中的对象有字面形式(比如`var a = { .. }`)和构造形式(比如`var a = new Array(..)`)。字面形式更常用，不过有时候构造形式可以提供更多选项。

对象就是键/值对的集合。可以通过`.propName`或者`["propName"]`语法来获取属性值。访问属性时，引擎实际上会调用内部的默认`[[Get]]`操作(在设置属性值时是`[[Put]])，[[Get]]`操作会检查对象本身是否包含这个属性，如果没找到的话还会查找`[[Prototype]]`链。

属性的特性可以通过属性描述符来控制，比如`writable`和`configurable`。此外，可以使用`Object.preventExtensions(..)`、`Object.seal(..)`和`Object.freeze(..)`来设置对象(及其 属性)的不可变性级别。

属性不一定包含值——它们可能是具备`getter/setter`的“访问描述符”。此外，属性可以是可枚举或者不可枚举的，这决定了它们是否会出现在`for..in`循环中。

你可以使用ES6的`for..of`语法来遍历数据结构(数组、对象，等等)中的值，`for..of`会寻找内置或者自定义的`@@iterator`对象并调用它的`next()`方法来遍历数据值。

## Chap4. 混合对象“类”

面向类的设计模式：

1. 实例化(instantitaion)
1. 继承(inheritance)
1. 多态(polymorphism)

### 4.1 类理论

> 面向对象编程强调的是数据和操作数据的行为本质上是相互关联的，好的设计就是把数据以及和它相关的行为封装起来。

举一个例子，“汽车”可以被看做“交通工具”的一种特例。

在软件中定义一个`Vehicle`类和一个`Car`类来对这种关系进行建模。

`Vehicle`的定义可能包含推进器（比如引擎）、载人能力等等。

定义`Car`时，只要声明它继承（或者扩展）了`Vehicle`的基础定义即可。`Car`的定义就是对通用`Vehicle`定义的特殊化。

虽然`Vehicle`和`Car`会定义相同的方法，但是实例中的数据可能是不同的，比如每辆车独一无二的VIN(Vehicle Identification Number)。

这便是类、继承和实例化。

类的另一个核心概念是多态，父类的通用行为可以被子类用更特殊的行为重写。

#### 4.1.1 “类”设计模式

面向对象设计模式包括：迭代器模式、观察者模式、工厂模式、单例模式，等等。

还有过程化编程和函数式编程。

#### 4.1.2 JavaScript中的类

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

> 从概念上说，子类可以通过相对多态引用(或者说super)来访问父类中的行为。子类得到的仅仅是继承自父类行为的一份副本。子类对继承到的一个方法进行“重写”，不会影响父类中的方法，这两个方法互不影响，因此才能使用相对多态引用访问父类中的方法(如果重写会影响父类的方法，那重写之后父类中的原始方法就不存在了，自然也无法引用)。

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
function mixin( sourceObj, targetObj ) {
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
        console.log( "Turning on my engine." );
    },
    drive: function() {
        this.ignition();
        console.log( "Steering and moving forward!" );
    }
};
var Car = mixin(Vehicle, {
    wheels: 4,
    drive: function() {
        Vehicle.drive.call( this );
        console.log("Rolling on all " + this.wheels + " wheels!");
    }
});
```

> 我们处理的已经不再是类了，因为在`JavaScript`中不存在类，`Vehicle`和`Car`都是对象，供我们分别进行复制和粘贴。

`Vehicle.drive.call(this)`这就是显式多态，之前的伪代码`inherited:drive()`称之为相对多态。

2.混合复制

```js
// 另一种混入函数，可能有重写风险
function mixin( sourceObj, targetObj ) {
    for (var key in sourceObj) {
        targetObj[key] = sourceObj[key];
    }
    return targetObj;
}

var Vehicle = { // ...
};

// 首先创建一个空对象并把 Vehicle 的内容复制进去
var Car = mixin( Vehicle, { } );

// 然后把新内容复制到 Car 中
mixin( {
    wheels: 4,
    drive: function() {
        // ...
    }
}, Car );
```

复制完成之后`Car`和`Vehicle`就分离了，但是两者要是引用同一个对象还是会相互影响对方。

还有寄生继承和隐式混入，详见p138

### 4.5 小结

`JavaScript`中的类与其他语言中的类完全不同。

类意味着复制。

传统的类被实例化时，它的行为会被复制到实例中。类被继承时，行为也会被复制到子类中。

多态(在继承链的不同层次名称相同但是功能不同的函数)看起来似乎是从子类引用父类，但是本质上引用的其实是复制的结果。

总的来说，在`JavaScript`中模拟类是得不偿失的，虽然能解决当前的问题，但是可能会埋下更多的隐患。

## 5. 原型

### 5.1 [[Prototype]]

```js
var anotherObject = {
    a:2
};

//创建一个关联到anotherObject的对象
var myObject = Object.create( anotherObject );

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

> 第二种情况可能是最令人意外的，只读属性会阻止`[[Prototype]]`链下层隐式创建(屏蔽)同名属性。这样做主要是为了模拟类属性的继承。你可以把原型链上层的`foo`看作是父类中的属性，它会被`myObject`继承(复制)，这样一来`myObject`中的`foo`属性也是只读，所以无法创建。但是一定要注意，实际上并不会发生类似的继承复制(参见第4章和第5章)。这看起来有点奇怪，`myObject`对象竟然会因为其他对象中有一个只读`foo`就不能包含`foo`属性。更奇怪的是，这个限制只存在于`=`赋值中，使用`Object.defineProperty(..)`并不会受到影响。第二种情况可能是最令人意外的，只读属性会阻止`[[Prototype]]`链下层隐式创建(屏蔽)同名属性。这样做主要是为了模拟类属性的继承。你可以把原型链上层的`foo`看作是父类中的属性，它会被`myObject`继承(复制)，这样一来`myObject`中的`foo`属性也是只读，所以无法创建。但是一定要注意，实际上并不会发生类似的继承复制(参见第4章和第5章)。这看起来有点奇怪，`myObject`对象竟然会因为其他对象中有一个只读`foo`就不能包含`foo`属性。更奇怪的是，这个限制只存在于`=`赋值中，使用`Object.defineProperty(..)`并不会受到影响。

下面代码会发生隐式屏蔽

```js
var anotherObject = {
    a:2
};

var myObject = Object.create( anotherObject );

another.a; // 2
myObject.a; // 2

anotherObject.hasOwnProperty( "a" ); // true
myObject.hasOwnProperty( "a" ); // false

myObject.a++; // 隐式屏蔽！

anotherObject.a; // 2
myObject.a; // 3

myObject.hasOwnProperty( "a" ); // true
```

`++`操作符相当于`myObject.a = myObject.a + 1`。`++`操作符首先会通过`[[Protitype]]`查找属性`a`并从`anotherObject.a`获取当前属性值2，然后给这个值加1，接着用`[[Put]]`将值3赋给`myObject`中新建的屏蔽属性`a`。

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

Object.getPrototypeOf( a ) === Foo.prototype; // true
```

回顾一下`new`操作：

1. 创建(或者说构造)一个全新的对象。
1. 这个新对象会被执行[[原型]]连接。
1. 这个新对象会绑定到函数调用的`this`。
1. 如果函数没有返回其他对象，那么`new`表达式中的函数调用会自动返回这个新对象。

调用`new Foo()`时会创建a，其中的一步就是给a一个内部的`[[Prototype]]`链接，关联到`Foo.prototype`指向的那个对象。

面向类的语言中，实例化（或者继承）一个类就意味着“把类的行为复制到物理对象中”，对于每一个新实例来说都会重复这个过程。

在`JavaScript`中我们没有初始化一个类，只是让两个对象相互关联，`JavaScript`中只能创建多个对象，它们的`[[Prototype]]`关联的是同一个对象。

##### 关于名称

从视觉角度来说，`[[Prototype]]`机制如下图所示。

![prototype机制](http://ovyn493ey.bkt.clouddn.com/prototype.png)

这个机制通常被称为原型继承，它常常被视为动态语言版本的类继承。

继承意味着复制操作，`JavaScript`(默认)并不会复制对象属性。相反，`JavaScript`会在两个对象之间创建一个关联，这样一个对象就可以通过委托访问另一个对象的属性和函数。委托(参见第6章)这个术语可以更加准确地描述`JavaScript`中对象的关联机制。

#### “构造函数”

