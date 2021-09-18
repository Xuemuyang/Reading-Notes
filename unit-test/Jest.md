# Jest

## 疑问

- [x]测试文件的后缀 `*.text.js` 和 `*.spec.js` 有什么区别？
  - [方应杭博客](https://github.com/FrankFang/gulu/issues/2)
- jest 的配置
- 如何单独对一个文件进行测试
  - 单独对一个测试进行测试 test.only API
- 测试如何 debug
  - 在开发过程中想及时的进行调试
  - 目前发现的是将测试用例给 fail，然后通过插件的 debug 来进入代码断点
- 与编辑器插件配合
- watch 功能如何使用
- 提供简单的代码片段进行参考

## 记录

在 type=module 的 node 项目中使用 jest，需要引入 babel，且 babel 配置文件的扩展名为 .cjs，才可以正常测试

## 基本介绍

介绍直接看官网，一个词概括就是 delightful 用起来非常简单愉快

可以和市面上众多的东西结合起来使用 Babel、TS、Node、三大框架

标榜的特性

- zero config 零配置就可以运行在大多数 JS 项目上
- snapshots 方便生成快照来追踪
- isolated 测试并行运行，性能好
  - 并且测试之间相互隔离
- great api 文档友好、持续维护
- code coverage 提供了测试覆盖率的功能
- easy mocking 通过丰富的 Mock 函数功能可以方便的 mock 函数内部调用的外部对象
- great exceptions 丰富的报错信息
- 测试文件，和用例的编排过程

哲学

- 文档丰富
- 简单配置
- 扩展性强

## 起步

1.安装

```bash
yarn add --dev jest
```

2.编写测试用例

对于 commonjs 的模块，直接 require 模块，使用 jest 提供的 API 编写用例

3.运行测试命令

jest 提供了一个命令行工具，不论是 npm scripts 还是直接通过命令行调用，使用的都是这个命令行工具

## 配置

虽说主打零配置，但是这肯定是不行的，这个配置可以 `jest` 作为 key 写在 `package.json` 中，也可以单独使用一个文件 `jest.config.js`

通过命令行来初始化这个工具 `jest --init`

### 配合 Babel 使用

这就有一个问题，什么情况下需要配合 Babel 使用

当需要测试的模块使用 es module 编写，这个时候我们就需要使用 babel 来测试

再安装一堆包

```sh
yarn add --dev babel-jest @babel/core @babel/preset-env
```

然后加入 babel 的配置

```js
// babel.config.js
module.exports = {
  presets: [
    [
      '@babel/preset-env',
      {
        targets: {
          node: 'current',
        },
      },
    ],
  ],
};
```

这么一配置下来，就可以测试 es module 的代码了

## 概念和用法

### Matchers

`expect` 这个 API 返回一个 "expectation" 对象，调用这个对象的一系列方法看结果是否符合预期

- `toBe` 判断基本类型的值是否相等，或者判断引用是否相同，原理 `Object.is`
- `toEqual` 判断对象 deep equal
- `toBeDefined` 值不是 `undefined`
- `toBeFalsy` 值为 falsy
- `toBeTruthy` 值为 truthy
- `toBeNull` 值为 `null`
- `toBeNaN` 值为 `NaN`

这里再看一下 falsy 的值：

- `false`
- `0`
- `''`
- `null`
- `undefined`
- `NaN`

如果要测试结果非，通常会这么写

```js
expect(z).not.toBeUndefined();
```

每一种类型的值会有不同的 matcher API，具体参考 [expect API](https://jestjs.io/docs/en/expect)

### 测试异步代码

与 JS 中实现异步的方式相似，Jest 提供了三种异步测试的方式

- Callbacks
- Promises
- Async/Await

直接看代码片段

#### Callbacks

传入一个叫 `done` 参数，Jest 会以 `done` 调用来结束测试

```js
test('the data is peanut butter', done => {
  function callback(data) {
    try {
      expect(data).toBe('peanut butter');
      done();
    } catch (error) {
      done(error);
    }
  }

  fetchData(callback);
});
```

#### Promises

我们的测试函数 `return` 一个 Promise

```js
test('the data is peanut butter', () => {
  return fetchData().then(data => {
    expect(data).toBe('peanut butter');
  });
});
```

在测试 rejected Promise 的时候通常需要用到 `expect.assertions()` 这个 API，这个 API 表明 `expect` 被调用了几次，通常用在异步代码的测试中，因为 fulfilled Promise 这个情况往往会让用例通过。

```js
test('the fetch fails with an error', () => {
  expect.assertions(1);
  return fetchData().catch(e => expect(e).toMatch('error'));
});
```

另外 Promise 有拆箱方法 `.resolves` 和 `.rejects`，直接看代码

```js
test('the data is peanut butter', () => {
  return expect(fetchData()).resolves.toBe('peanut butter');
});

test('the fetch fails with an error', () => {
  return expect(fetchData()).rejects.toMatch('error');
});
```

#### Async/Await

直接看代码，这个非常好理解

```js
test('the data is peanut butter', async () => {
  const data = await fetchData();
  expect(data).toBe('peanut butter');
});

test('the fetch fails with an error', async () => {
  expect.assertions(1);
  try {
    await fetchData();
  } catch (e) {
    expect(e).toMatch('error');
  }
});

test('the data is peanut butter', async () => {
  await expect(fetchData()).resolves.toBe('peanut butter');
});

test('the fetch fails with an error', async () => {
  await expect(fetchData()).rejects.toThrow('error');
});
```

### Setup 和 Teardown

这里就是洋葱模型，在测试执行之前干一些事情，执行之后干一些事情

大概提供了这些方法

- `beforeEach` 在作用域内每次 test 之前都会执行
- `afterEach`
- `beforeAll` 在作用域内只执行一次
- `afterAll`

这里有一个作用域的概念

如果直接写在文件内就是对整个文件生效，可以通过 `describe` 来进行一个分组，执行顺序和作用域看下面这段代码非常清晰

```js
beforeAll(() => console.log('1 - beforeAll'));
afterAll(() => console.log('1 - afterAll'));
beforeEach(() => console.log('1 - beforeEach'));
afterEach(() => console.log('1 - afterEach'));
test('', () => console.log('1 - test'));
describe('Scoped / Nested block', () => {
  beforeAll(() => console.log('2 - beforeAll'));
  afterAll(() => console.log('2 - afterAll'));
  beforeEach(() => console.log('2 - beforeEach'));
  afterEach(() => console.log('2 - afterEach'));
  test('', () => console.log('2 - test'));
});

// 1 - beforeAll
// 1 - beforeEach
// 1 - test
// 1 - afterEach
// 2 - beforeAll
// 1 - beforeEach
// 2 - beforeEach
// 2 - test
// 2 - afterEach
// 1 - afterEach
// 2 - afterAll
// 1 - afterAll
```

### Mock Functions

使用 mock 函数可以不去实际执行函数代码

有两种 mock 函数：

- 创建一个 mock 函数来用于测试
- 或者是 Manual Mock 这种方式来覆盖测试函数内部引用的模块，这种方式通常是需要在目录结构中创建一个 `__mocks__` 的目录来覆盖模块

参考:

- [Mock Functions](https://jestjs.io/docs/en/mock-functions)
- [Manual Mocks](https://jestjs.io/docs/en/manual-mocks)
- [Mock Functions - API](https://jestjs.io/docs/en/mock-function-api#mockfnmockclear)

使用 `jest.fn` 创建 mock 函数，这个情况多用于函数当参数传入的情况，可以通过 `.mock` 属性来获取函数的参数，调用信息，也可以使用 `mockReturnValueOnce`、`mockReturnValue` 等 API 来指定每一次调用的返回值

使用 `jest.mock` API 来 mock 函数内部引用的模块，比如看这个例子

```js
// users.js
import axios from 'axios';

class Users {
  static all() {
    return axios.get('/users.json').then(resp => resp.data);
  }
}

export default Users;
```

```js
// users.test.js
import axios from 'axios';
import Users from './users';

jest.mock('axios');

test('should fetch users', () => {
  const users = [{name: 'Bob'}];
  const resp = {data: users};
  axios.get.mockResolvedValue(resp);

  // or you could use the following depending on your use case:
  // axios.get.mockImplementation(() => Promise.resolve(resp))

  return Users.all().then(data => expect(data).toEqual(users));
});
```

上面的例子是 mock 返回值，还可以 mock 函数的执行过程

```js
// foo.js
module.exports = function () {
  // some implementation;
};

// test.js
jest.mock('../foo'); // this happens automatically with automocking
const foo = require('../foo');

// foo is a mock function
foo.mockImplementation(() => 42);
foo();
// > 42
```

与 `jest.fn` 类似，mock 函数的执行过程也有很多 mock 方法，可以设置第一次怎么执行，第二次怎么执行

## 参考

- [jest 官方文档](https://jestjs.io/)
- [顶级测试框架Jest指南：跑通一个完美的程序，就是教出一群像样的学生](https://segmentfault.com/a/1190000016399447)
- [[Jest]单元测试初学者指南 - Part1 - 函数测试](https://www.w3ctech.com/topic/2172)
  - 这是一个英文的系列
- [[译] Jest 入门教程：使用 Jest 运行 JS 单元测试](https://juejin.im/post/6844904030972411912)
- [令人愉快的 JavaScript 测试](https://juejin.im/post/6844903689799335943)
  - 这个文档中也有非常多的其他文章
- TS 课程中的单元测试

## 使用

- `describe` 方法用来定义一组测试，支持测试
- `test` 函数用来定义单个测试用例，是最小的测试单元，别名 `it`
- `expect` 是断言函数
