# Jest

## 疑问

- 测试文件的后缀 `*.text.js` 和 `*.spec.js` 有什么区别？
  - [方应杭博客](https://github.com/FrankFang/gulu/issues/2)
- 如何单独对一个文件进行测试
- 测试如何 debug
  - 在开发过程中想及时的进行调试
- 与编辑器插件配合
- mock 功能的探索
- watch 功能如何使用
- 提供简单的代码片段进行参考

## 参考

- [jest-doc](https://jestjs.io/docs/en/getting-started.html)

## 使用

- `describe` 方法用来定义一组测试，支持测试
- `test` 函数用来定义单个测试用例，是最小的测试单元，别名 `it`
- `expect` 是断言函数

## 类型相关

falsy 的值：

- `false`
- `0`
- `''`
- `null`
- `undefined`
- `NaN`

## 常用 API

- `toBe` 判断基本类型的值是否相等，或者判断引用是否相同，原理 `Object.is`
- `toEqual` 判断对象 deep equal
- `toBeDefined` 值不是 `undefined`
- `toBeFalsy` 值为 falsy
- `toBeTruthy` 值为 truthy
- `toBeNull` 值为 `null`
- `toBeNaN` 值为 `NaN`
