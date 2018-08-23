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
- 某类型数组 -> 某类型[] Array<某类型>
- boolean
- 元组
- any

方法也是如此

```ts
hello() : void{
  console.log('hello');
}
```

### 类型转换

两种写法

```ts
<string>hehe.type;

hehe.type as string;
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
> public 表示共有资产，谁想拿，去问class的示例拿就好了。

`?`代表可选属性，可传可不传

```ts
interface Person {
  readonly name: string;
  gender?: string;
}
```