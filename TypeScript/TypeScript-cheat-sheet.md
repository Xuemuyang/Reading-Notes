# TypeScript Cheat Sheet

- 类型定义文件如何指定

## 基础类型

```ts
let isDone: boolean = false;
let decLiteral: number = 6;
let myName: string = "Tom";
let u: undefined = undefined;
let n: null = null;
```

### 数组

两种方式

- 元素类型 + []
- 数组泛型

```ts
let list: number[] = [1, 2, 3]
let list: Array<number> = [1, 2, 3]
```

### 元组

一个已知数量和类型的数组，各元素类型不必相同

```ts
let x: [string, number]
x = ['hello', 10]
```

### 枚举

```ts
enum Days {
  Sun,
  Mon,
}

console.log(Days["Sun"] === 0); // true
console.log(Days["Mon"] === 1); // true

console.log(Days[0] === "Sun"); // true
console.log(Days[1] === "Mon"); // true
```

### 接口

```ts
interface Person {
  name: string;
  age?: number;
}
```

- `?` 可选属性
- `[propName: string]` 任意属性
- `readonly` 只读属性，可以理解为是变量与 `const` 的关系
- `[index: number]`
- `<T>(arg: T): T`

接口描述函数

```ts
interface SearchFunc {
  (source: string, subString: string): boolean;
}
```

## 类型断言

```ts
// 尖括号语法
let someValue: any = 'this is a string'
let strLength: number = (<string>someValue).length

// as 语法
let someValue: any = 'this is a string'
let strLength: number = (someValue as string).length
```

## 函数

- 函数声明
- 函数表达式
- 对象里的函数表达
- 对象里的函数省略表达(ES6)
- 箭头函数

函数声明

```ts
function sum(x: number, y: number): number {
  return x + y;
}
```

函数表达式

```ts
let mySum: (x: number, y: number) => number = function(
  x: number,
  y: number
): number {
  return x + y;
};
```

箭头函数

```ts
const sum = (x: number, y: number): number => {
  return x + y;
}
```

接口定义函数

```ts
interface SearchFunc {
  (source: string, subString: string): boolean;
}
```

## 参考
