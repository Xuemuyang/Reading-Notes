# [DVA](https://dvajs.com/)

## 疑问

- what
  - 简化 API，将 React-Router + Redux + Redux-saga 集成在了一起
- why
- how
  - 一个简单的用法

```jsx
const app = dva();

// 新增这一行
app.model({ /**/ });

app.router(() => <App />);
app.start('#root');
```

```js
{
  namespace: 'count',
  state: 0,
  reducers: {
    add(state) { return state + 1 },
  },
  effects: {
    *addAfter1Second(action, { call, put }) {
      yield call(delay, 1000);
      yield put({ type: 'add' });
    },
  },
}
```

`call` 和 `put` 都是 effect 函数内部的处理函数

- `call` 执行异步函数
- `put` 发出一个 Action,类似 dispatch

- redux
  - reducer
- mobx
- redux-thunk
- redux-saga
- react-redux
  - 提供了一个 connect 函数，将视图和数据连接起来，类比于 vuex map 相关 API

## Thinking

明确每一个函数的方法签名

## 记录
