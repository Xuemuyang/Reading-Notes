# React Hooks

## 疑问

- 什么是 Hook
- Hook 的本质是什么
- Hook 的原理
  - 目前个人对 Hook 的理解，
- 有哪些 Hook
  - useState
  - useEffect
- Hook 使用的限制(规则)
  - 只能在函数最外层调用
  - 只能在 React 函数组件中调用
- Hook 使用的代码片段

- linter 插件

[ ] 类组件的生命周期 didmount didupdate

## 代码片段

目前对 hook 的理解，react 内部通过高阶函数给这个函数式组件又包了一层，通过闭包创建了一个变量，这个变量可以在模板内部访问到，并且可以访问修改这个变量的函数，赋予这个变量初始值

```jsx
import React, { useState } from "react";
function Example() {
  // count 就是这个变量名，通过 count 可以访问到这个变量
  // setCount 是函数名，通过这个函数可以改变 count 的值
  // useState 则是 Hook 函数，这个函数的参数是 count 默认值
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

## 参考

- [Introducing Hooks](https://reactjs.org/docs/hooks-intro.html)
- [Hook 简介](https://zh-hans.reactjs.org/docs/hooks-intro.html)
- [React Hooks 详解 【近 1W 字】+ 项目实战](https://juejin.im/post/6844903985338400782)
