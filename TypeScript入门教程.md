# TypeScript 入门教程

## 疑问

- `[propName: string]` 是个什么玩意

## 简介

```sh
npm install -g typescript
```

```sh
tsc hehe.ts
```

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
- `readonly` 只读属性

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
```

数组的项中不允许出现其他的类型。

用接口表示数组

```ts
interface NumberArray {
  [index: number]: number;
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
```

### 函数的类型

