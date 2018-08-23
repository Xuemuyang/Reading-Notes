# TypeScript

## 基础认识

JavaScript 的超集

```sh
npm i -g typescript
```

会安装`tsc`这个命令，是 typescript compile 的缩写。

```sh
tsc hehe.ts // 编译成JS
tsc hehe.ts -d // 编译切生成d.ts文件
```

微软主推 C#语言，typescript 的语法与 C#差异不大。

typeScript 的代码提示秘密在于 d.ts 文件。

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
let list: number[] = [1, 2, 3];
let list: Array<number> = [1, 2, 3]; // 数组泛型
```

#### 元组 Tuple

元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。

```ts
let x: [string, number];
x = ["hello", 10];
```

#### 枚举

使用枚举类型的一个便利是可以由枚举的值得到它的名字。

默认情况下从 0 开始为元素编号，可以手动指定成员的数值。

```ts
enum Color {
  Red = 1,
  Green,
  Blue
}
let colorName: string = Color[2];

alert(colorName); // 显示'Green'因为上面代码里它的值是2
```

方法也是如此

```ts
hello() : void{
  console.log('hello');
}
```

### 类型转换(断言)

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

如果没有明确的指定类型，TypeScript会依照类型推论(Type Inference)的规则推断出一个类型。

```ts
let myFavoriteNumber = 'seven';
myFavoriteNumber = 7;

// 等价于

let myFavoriteNumber: string = 'seven';
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
myFavoriteNumber = 'seven';
console.log(myFavoriteNumber.length); // 5
myFavoriteNumber = 7;
console.log(myFavoriteNumber.length); // 编译时报错

// index.ts(5,30): error TS2339: Property 'length' does not exist on type 'number'.
```

## 接口与类

接口的作用就是去描述结构的形态。

### 属性修饰符

- `readonly`
- `private`
- `protected`
- `public` 默认
- `?`

`readonly`只读属性，不能被修改，初始化的时候必须要赋值。

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

### 描述函数

```ts
interface Db {
  host: string;
  port: number;
}

interface InitFunc {
  (options: Db): string;
}

let myfunc: InitFunc = function(opts: Db) {
  return "";
};
```

InitFunc 接口规定，它的函数调用需要传递一个 Db 合同、约束的 options 对象，返回一个 string 类型的值。

## 函数

```ts
let add1 = function(x: number, y: number): number {
  return x + y;
};

function add2(x: number, y: number): number {
  return x + y;
}
```

函数参数的类型，返回值的类型

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
