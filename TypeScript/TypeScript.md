# TypeScript 入门教程

## 疑问

- 签名的对象字面量
- 函数重载是否只是校验类型
- 关于类型文件的目录
- TS里面函数的几种写法总结

[Typescript-发布](https://www.tslang.cn/docs/handbook/declaration-files/publishing.html)章节中有介绍，包含声明文件一起发布 npm 包，在 package.json 中指定主声明文件，`types` 和 `typings` 具有相同的意义。

- tsconfig.json

ts 的配置文件中指定了 `types`，只有被列出来的包才会被包含进来。

代码片段1：

```ts
interface Person {
  name: string;
  age: number;
}

let person: Person = {
  name: 'haha',
  age: 123,
  gender: 'man'
}

function makeFriend(friend: Person) {
  console.log(friend);
}

let person1 = {
  name: 'haha',
  age: 123,
  gender: 'man'
}

makeFriend(person1);
```

代码片段2：

类型断言绕过检查

```ts
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare (config: SquareConfig): { color: string; area: number } {
  let newSquare = {color: 'white', area: 100}
  if (config.color) {
    newSquare.color = config.color
  }
  if (config.width) {
    newSquare.area = config.width * config.width
  }
  return newSquare
}

let mySquare = createSquare({ width: 100, opacity: 0.5 } as SquareConfig) // 加了类型断言之后就可以过编译了
```

- 属性名后面的 `!`

非空断言，TS 判断可能为空，开发者使用 `!` 告诉 TS 不为空

## 简介

```sh
npm install -g typescript
```

```sh
tsc hehe.ts
```

微软主推 C#语言，typescript 的语法与 C#差异不大。

TypeScript 文件以 `.ts` 为后缀。

TypeScript 最大的优势便是增强了编辑器和 IDE 的功能，包括代码补全、接口提示、跳转到定义、重构等。

TypeScript 只会进行静态检查，如果发现有错误，编译的时候就会报错。

## 基础

### 原始数据类型（Basic Types）

JS 中的五种基本数据类型(primitive data types)

```ts
let isDone: boolean = false;
let decLiteral: number = 6;
let myName: string = "Tom";
let u: undefined = undefined;
let n: null = null;
```

```ts
// void 表示没有任何返回值的函数
function alertName(): void {
  alert("My name is Tom");
}
// 显式声明 void 作用不大，只能将其赋值为 undefined 或者 null
let unusable: void = undefined;

// `undefined` 和 `null` 是所有类型的子类型
let num: number = undefined;
let u: undefined;
```

### 任意值（Any）

任意值（Any）用来表示允许赋值为任意类型。

变量如果在声明的时候，未指定其类型，那么它会被识别为任意值类型。

下面这两端代码等价。

```ts
let something;
something = "seven";
something = 7;
```

```ts
let something: any;
something = "seven";
something = 7;
```

### 类型推论（Type Inference）

如果没有明确的指定类型，那么 TypeScript 会依照类型推论（Type Inference）的规则推断出一个类型。

```ts
let myFavoriteNumber = "seven";
myFavoriteNumber = 7;
// 报错
```

如果定义的时候没有赋值，不管之后有没有赋值，都会被推断成 any 类型而完全不被类型检查：

```ts
let myFavoriteNumber;
myFavoriteNumber = "seven";
myFavoriteNumber = 7;
```

### 联合类型（Union Types）

联合类型（Union Types）表示取值可以为多种类型中的一种。

```ts
let myFavoriteNumber: string | number;
myFavoriteNumber = "seven";
myFavoriteNumber = 7;
```

当 TypeScript 不确定联合类型的变量到底是哪个类型的时候，只能访问联合类型里公共的属性和方法。

```ts
function getLength(something: string | number): number {
  return something.length;
}
// 报错
```

```ts
function getString(something: string | number): string {
  return something.toString();
}
```

```ts
let myFavoriteNumber: string | number;
myFavoriteNumber = "seven";
console.log(myFavoriteNumber.length); // 5
myFavoriteNumber = 7;
console.log(myFavoriteNumber.length); // 编译时报错
```

这里报错的原因是 TS 的类型推断，赋值 `seven` 之后被推断成了 `string`。

### 对象的类型---接口（Interfaces）

一般首字母大写，有的实践建议在接口中加上 `I` 前缀。

使用接口对对象的形状来描述

```ts
interface Person {
  name: string;
  age: number;
}

let tom: Person = {
  name: "Tom",
  age: 25
};
```

定义的变量比接口少属性，多属性都是不可以的，赋值的时候，变量的形状必须和接口的形状保持一致。

- `?` 可选属性
- `[propName: string]` 任意属性
- `readonly` 只读属性，可以理解为是变量与 `const` 的关系
- `[index: number]`
- `<T>(arg: T): T`

```ts
interface Person {
  name: string;
  age?: number;
}

let tom: Person = {
  name: "Tom"
};
```

```ts
interface Person {
  name: string;
  [propName: string]: string;
}
```

使用索引签名来描述那些能够“通过索引得到”的类型，可以是字符串索引，也可以是数字索引。

可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型。

使用 `[propName: string]` 定义了任意属性取 `string` 类型的值。需要注意的是，一旦定义了任意属性，那么确定属性和可选属性的类型都必须是它的类型的子集。

只读的约束存在于第一次给对象赋值的时候。

```ts
interface Person {
  readonly id: number;
  name: string;
  age?: number;
  [propName: string]: any;
}

let teemo: Person = {
  id: 1,
  name: "Teemo"
};
```

### 数组的类型

```ts
let fibonacci: number[] = [1, 1, 2, 3, 5];
let fibonacciStr: (number | string)[] = ["1", "1", "2", "3", "5"];
let list: Array<number> = [1, 2, 3];
```

数组的项中不允许出现其他的类型。

用接口表示数组

```ts
interface NumberArray {
  [index: number]: number;
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
```

### 函数

函数声明，输入多余的（或者少于要求的）参数，是不被允许的。

```ts
function sum(x: number, y: number): number {
  return x + y;
}
```

函数类型包含两部分：参数类型和返回值类型。

函数表达式，在 TS 中，`=>` 用来表示函数定义，左边是输入类型，需要用括号括起来，右边是输出类型。

```ts
let mySum: (x: number, y: number) => number = function(
  x: number,
  y: number
): number {
  return x + y;
};
```

用接口定义函数的形状

```ts
interface SearchFunc {
  (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
  return source.search(subString) !== -1;
};
```

使用 `?` 表示可选参数

```ts
function buildName(firstName: string, lastName?: string) {
  if (lastName) {
    return firstName + " " + lastName;
  } else {
    return firstName;
  }
}
let tomcat = buildName("Tom", "Cat");
let tom = buildName("Tom");
```

重载允许一个函数接受不同数量或类型的参数时，作出不同的处理。

```ts
function reverse(x: number | string): number | string {
  if (typeof x === "number") {
    return Number(
      x
        .toString()
        .split("")
        .reverse()
        .join("")
    );
  } else if (typeof x === "string") {
    return x
      .split("")
      .reverse()
      .join("");
  }
}
```

这样的缺点在于不能精准的表达，输入为`number`的时候，输出也应该为`number`，输入为`string`的时候，输出应该为`string`。

需要使用重载定义多个 reverse 的函数类型:

```ts
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string {
  if (typeof x === "number") {
    return Number(
      x
        .toString()
        .split("")
        .reverse()
        .join("")
    );
  } else if (typeof x === "string") {
    return x
      .split("")
      .reverse()
      .join("");
  }
}
```

#### this参数

使用 `--noImplicitThis` 就可以禁用 `this` 相关的类型检查。

typescript 提供一个显示的 `this` 参数，是一个假的参数，出现在参数列表的最前面：

```ts
function f(this: void) {
  // make sure `this` is unusable in this standalone function
}
```

```ts
interface Card {
  suit: string;
  card: number;
}
interface Deck {
  suits: string[];
  cards: number[];
  createCardPicker(this: Deck): () => Card;
}
let deck: Deck = {
  suits: ["hearts", "spades", "clubs", "diamonds"],
  cards: Array(52),
  // NOTE: The function now explicitly specifies that its callee must be of type Deck
  createCardPicker: function(this: Deck) {
    return () => {
      let pickedCard = Math.floor(Math.random() * 52);
      let pickedSuit = Math.floor(pickedCard / 13);

      return {suit: this.suits[pickedSuit], card: pickedCard % 13};
    }
  }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

在回调函数中 `this` 防止报错，需要手动指定 `this` 的类型为 `void`。

### 类型断言（Type Assertion）

```ts
function getLength(something: string | number): number {
  return something.length;
}
// 报错
```

某些情况下我们比 TS 编译器更了解某个值的类型，通过类型断言这种方式可以告诉编译器，“相信我，我知道自己在干什么”，TypeScript 会以为开发者已经进行了必须的检查。

有两种语法：

- <类型>值
- 值 as 类型

```ts
function getLength(something: string | number): number {
  if ((<string>something).length) {
    return (<string>something).length;
  } else {
    return something.toString().length;
  }
}
```

```ts
function getLength(something: string | number): number {
  if ((something as string).length) {
    return (something as string).length;
  } else {
    return something.toString().length;
  }
}
```

### 声明文件

使用第三方库需要引用声明文件，才能获得代码补全，接口提示的功能。

### 内置对象

ECMAScript 内置对象有（举个 🌰）：

- `Boolean`
- `Error`
- `Date`
- `RegExp`

DOM 和 BOM 的内置对象（举个 🌰）：

- `Document`
- `HTMLElement`
- `Event`
- `NodeList`

这些文件的定义在 TypeScript 核心库的定义文件中。

Node.js 不是内置对象的一部分。

## 进阶

### 类型别名（Type Aliases）

```ts
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
  if (typeof n === "string") {
    return n;
  } else {
    return n();
  }
}
```

类型别名常用与联合类型。

```ts
type EventNames = "click" | "scroll" | "mouseover";
```

这个例子中使用`type`定了一个字符串字面量类型 EventNames，它只能取三种字符串中的一种。

### 字符串字面量类型（String Literal Types）

```ts
type EventNames = "click" | "scroll" | "mousemove";
function handleEvent(ele: Element, event: EventNames) {
  // do something
}

handleEvent(document.getElementById("hello"), "scroll"); // 没问题
```

### 元组（Tuple）

元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。

```ts
let one: [string, number];
one[0] = "one";
one[1] = 1;

one[0].slice(1);
one[1].toFixed(2);
```

### 枚举（Enum）

枚举类型用于取值被限定在一定范围内的场景，如一周有七天，一年四季，颜色红蓝绿。

枚举成员会被赋值为从 0 开始递增的数字，同时也会对枚举值到枚举名进行反向映射：

```ts
enum Days {
  Sun,
  Mon,
  Tue,
  Wed,
  Thu,
  Fri,
  Sat
}

console.log(Days["Sun"] === 0); // true
console.log(Days["Mon"] === 1); // true
console.log(Days["Tue"] === 2); // true
console.log(Days["Sat"] === 6); // true

console.log(Days[0] === "Sun"); // true
console.log(Days[1] === "Mon"); // true
console.log(Days[2] === "Tue"); // true
console.log(Days[6] === "Sat"); // true
```

会被编译为:

```ts
var Days;
(function(Days) {
  Days[(Days["Sun"] = 0)] = "Sun";
  Days[(Days["Mon"] = 1)] = "Mon";
  Days[(Days["Tue"] = 2)] = "Tue";
  Days[(Days["Wed"] = 3)] = "Wed";
  Days[(Days["Thu"] = 4)] = "Thu";
  Days[(Days["Fri"] = 5)] = "Fri";
  Days[(Days["Sat"] = 6)] = "Sat";
})(Days || (Days = {}));
```

### 类（Classes）

类的相关概念

- 类(Class)：定义了一件事物的抽象特点，包含它的属性和方法
- 对象（Object）：类的实例，通过 new 生成
- 面向对象（OOP）的三大特性：封装、继承、多态
- 封装（Encapsulation）：将对数据的操作细节隐藏起来，只暴露对外的接口。外界调用端不需要（也不可能）知道细节，就能通过对外提供的接口来访问该对象，同时也保证了外界无法任意更改对象内部的数据
- 继承（Inheritance）：子类继承父类，子类除了拥有父类的所有特性外，还有一些更具体的特性
- 多态（Polymorphism）：由继承而产生了相关的不同的类，对同一个方法可以有不同的响应。比如 Cat 和 Dog 都继承自 Animal，但是分别实现了自己的 eat 方法。此时针对某一个实例，我们无需了解它是 Cat 还是 Dog，就可以直接调用 eat 方法，程序会自动判断出来应该如何执行 eat
- 存取器（getter & setter）：用以改变属性的读取和赋值行为
- 修饰符（Modifiers）：修饰符是一些关键字，用于限定成员或类型的性质。比如 public 表示公有属性或方法
- 抽象类（Abstract Class）：抽象类是供其他类继承的基类，抽象类不允许被实例化。抽象类中的抽象方法必须在子类中被实现
- 接口（Interfaces）：不同类之间公有的属性或方法，可以抽象成一个接口。接口可以被类实现（implements）。一个类只能继承自另一个类，但是可以实现多个接口

看一个例子

```ts
class Greeter {
  greeting: string
  constructor(message: string) {
    this.greeting = message
  }
  greet() {
    return 'Hello, ' + this.greeting
  }
}

let greeter = new Greeter('world')
```

这个类有三个成员：

- 属性
- 构造函数
- 方法

#### 静态方法

使用 `static` 修饰符修饰的方法称为静态方法，不需要实例化，直接通过类来调用。

#### 访问修饰符

- public 修饰的属性或方法是公有的，可以在任何地方被访问到，默认所有的属性和方法都是 public 的
- private 修饰的属性或方法是私有的，不能在声明它的类的外部访问
- protected 修饰的属性或方法是受保护的，它和 private 类似，区别是它在子类中也是允许被访问的

### 类与接口

接口还可以对类的一部分行为进行抽象。

有时候不同类之间可以有一些共有的特征。

一个类可以实现多个接口。

实现(implements)是面向对象中的一个重要概念。不同类之间可以有一些共有的特性，这时候就可以把特性提取成接口(interfaces)，用 implements 关键字来实现。

举例来说，门是一个类，防盗门是门的子类。如果防盗门有一个报警器的功能，我们可以简单的给防盗门添加一个报警方法。这时候如果有另一个类，车，也有报警器的功能，就可以考虑把报警器提取出来，作为一个接口，防盗门和车都去实现它：

```ts
interface Alarm {
  alert();
}

interface Light {
  lightOn();
  lightOff();
}

class Car implements Alarm, Light {
  alert() {
    console.log("Car alert");
  }
  lightOn() {
    console.log("Car light on");
  }
  lightOff() {
    console.log("Car light off");
  }
}
```

接口继承接口：

```ts
interface Alarm {
  alert();
}

interface LightableAlarm extends Alarm {
  lightOn();
  lightOff();
}
```

接口继承类：

```ts
class Point {
  x: number;
  y: number;
}

interface Point3d extends Point {
  z: number;
}

let point3d: Point3d = { x: 1, y: 2, z: 3 };
```

### 泛型（Generics）

泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。

> 类型实际上可以进行一定的运算，要想写出的类型适用范围更广，不妨让它像函数一样可以接受参数。TS 的泛型便是起到这样的作用，你可以把它当作类型的参数。它和函数参数一样，可以有默认值。 ---[《TypeScript 中高级应用与最佳实践》](http://www.alloyteam.com/2019/07/13796/)

`T` 是类型变量，可以捕获传入的类型。

```ts
function createArray<T>(length: number, value: T): Array<T> {
  let result: T[] = [];
  for (let i = 0; i < length; i++) {
    result[i] = value;
  }
  return result;
}

createArray<string>(3, "x"); // ['x', 'x', 'x']
```

泛型约束

```ts
interface Lengthwise {
  length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length);
  return arg;
}
```

### 高级类型

#### 交叉类型(Intersection Types)

将多个类型合并为一个类型

`Person & Serializable & Loggable` 同时是 `Person`、`Serializable`、`Loggable`。

#### 联合类型(Union Types)

`number | string | boolean` 表示一个值可以是 `number`、`string` 或 `boolean`。

当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们只能访问此联合类型的所有类型里共有的属性或方法：

```ts
function getLength(something: string | number): number {
  return something.length;
}

// index.ts(2,22): error TS2339: Property 'length' does not exist on type 'string | number'.
//   Property 'length' does not exist on type 'number'.
```

联合类型的变量在被赋值的时候，会根据类型推论的规则推断出一个类型：

```ts
let myFavoriteNumber: string | number;
myFavoriteNumber = "seven";
console.log(myFavoriteNumber.length); // 5
myFavoriteNumber = 7;
console.log(myFavoriteNumber.length); // 编译时报错

// index.ts(5,30): error TS2339: Property 'length' does not exist on type 'number'.
```

#### 类型保护

使用 if 判断语句对变量的类型进行检查，可以使用 `typeof`、`instanceof`、`in`、字面量类型、还有用户自定义的类型保护。

```ts
// 仅仅是一个 interface
interface Foo {
  foo: number;
  common: string;
}

interface Bar {
  bar: number;
  common: string;
}

// 用户自己定义的类型保护！
function isFoo(arg: Foo | Bar): arg is Foo {
  return (arg as Foo).foo !== undefined;
}

// 用户自己定义的类型保护使用用例：
function doStuff(arg: Foo | Bar) {
  // 如果通过了isFoo类型检查，后续的条件分支中arg就会被推断为Foo类型，获得编辑器提示等等
  if (isFoo(arg)) {
    console.log(arg.foo); // ok
    console.log(arg.bar); // Error
  } else {
    console.log(arg.foo); // Error
    console.log(arg.bar); // ok
  }
}

doStuff({ foo: 123, common: '123' });
doStuff({ bar: 123, common: '123' });
```
