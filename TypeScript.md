# TypeScript

## 基础认识

JavaScript 的超集

```sh
npm i -g typescript
```

会安装`tsc`这个命令，是 typescript compile 的缩写。

```sh
tsc hehe.ts // 编译成JS
tsc hehe.ts -d // 编译且生成d.ts文件
```

微软主推 C#语言，typescript 的语法与 C#差异不大。

### 声明文件

typeScript 的代码提示秘密在于 d.ts 文件，约定声明文件以.d.ts 为后缀。

```ts
// jQuery.d.ts
declare var jQuery: (string) => any;

/// <reference path="./jQuery.d.ts" />
```

可以使用@types 来管理第三方声明文件。

JavaScript 中的 DOM 和 BOM 内置对象在 TypeScript 核心库的定义文件中有定义。

如果用 TypeScript 来写 Node.js，需要引入第三方声明文件:

```sh
npm install @types/node --save-dev
```

## 基础类型

### 定义及类型声明

`let`定义变量

`const`定义常量

```ts
let 变量名称: 变量类型 = 变量的值;
let word: string = "how";
```

`:`的语义是对冒号之前的东西进行解释和阐述。

变量类型可以是:

- number
- string
- 某类型数组 -> 元素类型[] Array<元素类型>
- boolean
- 元组 Tuple
- any
- void

#### 数组

数组有两种定义方式

```ts
let list: number[] = [1, 2, 3]; // 类型[]
let list: Array<number> = [1, 2, 3]; // 数组泛型(Array Generic)
```

#### 元组 Tuple

元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。

```ts
let x: [string, number];
x = ["hello", 10];
```

#### 枚举 Enum

枚举类型用于取值被限定在一定范围内的场景，比如一周只有七天，颜色限定为红蓝绿等。

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

也可以给枚举手动赋值

### 类型转换(断言)

类型断言(Type Assertion)

两种写法

第一种是"尖括号"写法

```ts
let someValue: any = "this is a string";

let strLength: number = (<string>someValue).length;
```

另一个是`as`语法

```ts
let someValue: any = "this is a string";

let strLength: number = (someValue as string).length;
```

### 类型推论

如果没有明确的指定类型，TypeScript 会依照类型推论(Type Inference)的规则推断出一个类型。

```ts
let myFavoriteNumber = "seven";
myFavoriteNumber = 7;

// 等价于

let myFavoriteNumber: string = "seven";
myFavoriteNumber = 7;

// 都会报错
// index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
```

如果定义的时候没有赋值，不管之后有没有赋值，都会被推断成`any`类型而完全不被类型检查。

### 变量声明

`const`是对`let`的一个增强，能够阻止对一个变量再次赋值。

除了计划去修改的变量都应该使用`const`去声明。

### 联合类型(Union Types)

表示取值可以为多种类型中的一种。

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

### 类型别名

直接看栗子，使用`type`创建类型别名，类型别名常用于联合类型。

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

```ts
type EventNames = "click" | "scroll" | "mouseover";
```

这个例子中使用`type`定了一个字符串字面量类型 EventNames，它只能取三种字符串中的一种。

## 类

先来看一些面向对象的简单介绍

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

## 接口与类

接口的作用就是去描述结构的形态。

可以用接口来定义对象的类型。

接口可以对类的一部分行为进行抽象。

### 属性修饰符

- `readonly`
- `private`
- `protected`
- `public` 默认
- `?`

`readonly`只读属性，不能被修改，初始化的时候必须要赋值。即在对象创建的时候被赋值。

```ts
interface Person {
  readonly IdCard: string; // 身份证号
}

class Person implements Person {
  readonly IdCard: string = "42xxxxxxxxxxxxxxx";
  constructor() {}
}
```

也可以通过字面量构造类变量。

```ts
interface Person {
  readonly IdCard: string; // 身份证号
}

let person: Person = { IdCard: "43xxxxxxxxx" };
```

`private`是私有变量，不能被其他访问，只归自己管，而`protected`修饰的则可以被继承。

`public`是默认修饰符。

> 对于属性修饰符可以这么理解
> `class`代表着一个家族成员，`extend`表示血缘关系，就像上面的父亲与儿子。
> `private`是属于家族成员的私有物品，私人空间，别人是不能看到的，除非自己告诉别人，通过方法返回。
> `protected`表示家族资产，比如姓氏，某一宝物，古董。
> public 表示共有资产，谁想拿，去问 class 的示例拿就好了。

`?`代表可选属性，可传可不传

```ts
interface Person {
  readonly name: string;
  gender?: string;
}
```

### 任意属性

一旦定义了任意属性，那么确定属性和可选属性都必须是它的子属性

```ts
interface Person {
  name: string;
  age?: number;
  [propName: string]: any;
}

let tom: Person = {
  name: "Tom",
  gender: "male"
};
```

### 类实现接口

实现(implements)是面向对象中的一个重要概念。不同类之间可以有一些共有的特性，这时候就可以把特性提取成接口(interfaces)，用 implements 关键字来实现。

举例来说，门是一个类，防盗门是门的子类。如果防盗门有一个报警器的功能，我们可以简单的给防盗门添加一个报警方法。这时候如果有另一个类，车，也有报警器的功能，就可以考虑把报警器提取出来，作为一个接口，防盗门和车都去实现它：

```ts
interface Alarm {
  alert();
}

class Door {}

class SecurityDoor extends Door implements Alarm {
  alert() {
    console.log("SecurityDoor alert");
  }
}

class Car implements Alarm {
  alert() {
    console.log("Car alert");
  }
}
```

一个类可以实现多个接口:

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

上例中，Car 实现了 Alarm 和 Light 接口，既能报警，也能开关车灯。

### 接口继承接口

接口与接口之间可以是继承关系：

```ts
interface Alarm {
  alert();
}

interface LightableAlarm extends Alarm {
  lightOn();
  lightOff();
}
```

### 接口继承类

接口也可以继承类:

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

## 函数

### 函数声明

TypeScript 将函数的输入输出都考虑到

```ts
function add2(x: number, y: number): number {
  return x + y;
}
```

传参数的个数不对过不了编译

### 函数表达式

```ts
let mySum = function(x: number, y: number): number {
  return x + y;
};
```

这样可以过编译，但是没有对等号左边的类型定义，而是通过赋值操作进行类型推论推断出来的。

```ts
let mySum: (x: number, y: number) => number = function(
  x: number,
  y: number
): number {
  return x + y;
};
```

在 TypeScript 的类型定义中，`=>`用来表示函数的定义，左边是输入类型，需要用括号括起来，右边是输出类型。

这与 ES6 中的`=>`差别很大

使用接口的方式来定义一个函数

```ts
interface SearchFunc {
  (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
  return source.search(subString) !== -1;
};
```

### 可选参数

```ts
function buildName(firstName: string, lastName?: string) {
  if (lastName) return firstName + " " + lastName;
  else return firstName;
}

let result1 = buildName("Bob"); // works correctly now
let result2 = buildName("Bob", "Adams", "Sr."); // error, too many parameters
let result3 = buildName("Bob", "Adams"); // ah, just right
```

### 重载

重载允许一个函数接受不同数量或类型的参数时，做出不同的处理。

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

## 泛型

泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。

类型变量是一种特殊的变量，只用于表示类型而不是值。

```ts
function identity<T>(arg: T): T {
  return arg;
}
```

使用类型变量 `T` 来捕获用户传入的类型，之后就可以使用类型变量。
