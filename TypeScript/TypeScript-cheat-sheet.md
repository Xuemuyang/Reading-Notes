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
let list: number[] = [1, 2, 3];
let list: Array<number> = [1, 2, 3];
```

## 联合/交叉类型

- 联合(Union Types) `|` 满足任意一个即可
- 交叉(Intersection Types) `&` 必须同时满足多个

```ts
interface IA {
  a: string;
  b: number;
}

type TB = {
  b: number;
  c: number[];
};

type TC = IA | TB; // TC类型的变量的键只需包含ab或bc即可，当然也可以abc
type TD = IA & TB; // TD类型的变量的键必需包含abc
```

### 元组

一个已知数量和类型的数组，各元素类型不必相同

```ts
let x: [string, number];
x = ["hello", 10];
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
let someValue: any = "this is a string";
let strLength: number = (<string>someValue).length;

// as 语法
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;
```

非空断言(Non-null assertion operator)，断言不为 `undefined` 或 `null`

```ts
X.getY()!.a()
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
let mySum: (x: number, y: number) => number = function (
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
};
```

接口定义函数

```ts
interface SearchFunc {
  (source: string, subString: string): boolean;
}
```

## 工具类

`Partial` 为所有属性添加 `?`，`Required` 则是将所有属性都设置为必填。

```ts
interface Person {
  name: string;
  age: number;
}

type NewPerson = Partial<Person>;
const P: NewPerson = {
  name: "joy",
};
// name和age都被设置成了可选属性
```

`Record` 给定 key:value 形式

```ts
type r = Record<"a" | "b" | "c", Person>;
// 最终实现{a:Person;b:Person;c:Person}
```

## 工具

配置 VSCode 单 .ts 文件调试环境

```json
// .vscode/launch.json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Current TS File",
      "type": "node",
      "request": "launch",
      "program": "${workspaceRoot}/node_modules/ts-node/dist/bin.js",
      "args": ["${relativeFile}"],
      "cwd": "${workspaceRoot}",
      "protocol": "inspector"
    }
  ]
}
```

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "strict": true,
    "allowJs": true,
    "esModuleInterop": true
  }
}
```

## 参考
