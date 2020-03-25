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

接口定义函数

```ts
interface SearchFunc {
  (source: string, subString: string): boolean;
}
```
