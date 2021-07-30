# redux

## Flux

APPLICATION ARCHITECTURE FOR BUILDING USER INTERFACES

Flux 将一个应用分成四个部分

- View: 视图层
- Action: 视图层发出的消息
- Dispatcher: 用来接收 Actions、执行 callback
- Store: 用来存放应用状态，一旦发生变动，提醒 Views 更新页面

![Flux](./images/Redux/1.png)

Flux 最大的特点是数据单向流动

1. 用户访问 View
2. View 发出用户的 Action
3. Dispatcher 收到 Action，要求 Store 进行相应更新
4. Store 更新后，发出一个"change"事件
5. View 收到"change"事件后，更新页面

### Action

```js
AppDispatcher.dispatch({
  actionType: "ADD_NEW_ITEM",
  text: text,
});
```

每个 Action 都是一个对象，包含一个`actionType`属性(说明动作的类型)和 payload 即一些其他属性(用来传递数据)。

### Dispatcher

Dispatcher 的作用是将 Action 派发到 Store，可以将其看做是一个路由器，负责在 View 和 Store 之间建立 Action 的正确传递路线。

## 废话

Redux 使用场景

- 用户的使用方式复杂
- 不同身份的用户有不同的使用方式（比如普通用户和管理员）
- 多个用户之间可以协作
- 与服务器大量交互，或者使用了 WebSocket
- View 要从多个来源获取数据

从组件角度的使用场景

- 某个组件的状态，需要共享
- 某个状态需要在任何地方都可以拿到
- 一个组件需要改变全局状态
- 一个组件需要改变另一个组件的状态

设计思想

1. Web 应用是一个状态机，视图与状态是一一对应的。
2. 所有的状态，保存在一个对象里面。

## 基础

### Store

Store 是保存数据的地方，Redux 应用只有一个单一的 store。

```js
import { createStore } from "redux";
import todoApp from "./reducers";
let store = createStore(todoApp);
```

Store 提供了三个方法

- `store.getState()`
- `store.dispatch()`
- `store.subscribe()`

Store 允许`store.subscribe`设置监听函数，一旦 State 发生变化，就自动执行函数。将 View 的更新函数放入其中就会实现 View 的自动渲染。

应用状态默认值可以在`createStore`的第二个参数中传入，也可以在`Reducer`的默认初始值给出，看场景选用。

### State

`Store`对象的快照叫做 State。

当前时刻的 State 可以用`store.getState()`拿到。

```js
import { createStore } from "redux";
import todoApp from "./reducers";
let store = createStore(todoApp);

const state = store.getState();
```

Redux 规定一个 State 对应一个 View。

### Action

Action 是把数据从应用传到 Store 的有效荷载，是 Store 数据的唯一来源。

约定 action 内必须使用一个字符串类型的`type`字段来表示将要执行的动作，多数情况下，`type`会被定义成字符串常量。建议使用调度的模块或文件来存放 actionTypes。

```js
import { ADD_TODO, REMOVE_TODO } from "../actionTypes";
```

Action 创建函数(Action Creator)

```js
function addTodo(text) {
  return {
    type: ADD_TODO,
    text,
  };
}

const action = addTodo("Learn Redux");
```

通过调用`store.dispatch()`将 Action 发出去。

### Reducer

Store 收到 Action 之后，必须给出一个新的 State，这样 View 才会发生变化。这种 State 的计算过程就叫做 Reducer。

Reducer 是一个纯函数，接收旧的 state 和 action，返回新的 state。

纯函数可以保证同样的 State，必定得到同样的 View。

```js
(previousState, action) => newState;
```

Reducer 不需要手动调用，`store.dispatch`方法触发 Reducer 自动执行。

```js
import { createStore } from "redux";
let store = createStore(reducer);
```

每当`store.dispatch`发送过来一个新的 Action，就会自动调用 Reducer，得到新的 State。

#### Reducer 的拆分

使用 Redux 提供的`combineReducers`方法，将子 Reducer 函数合成一个大的 Reducer。

### 数据流

严格的单向数据流是 Redux 架构的设计核心

![redux-flow](./images/Redux/redux-flow.jpg)

1. 调用`store.dispatch(action)`，发出 Action
1. Redux store 自动调用 Reducer，并传入当前的 State 和收到的 Action，Reducer 返回新的 State
1. State 一旦有变化，Store 就会调用监听函数
1. 根 Reducer 应该把多个子 Reducer 输出合并成一个单一的 State 树
1. Redux store 保存了根 Reducer 返回的完整 State 树

## 中间件和异步操作

异步操作思路

- 操作开始时，送出一个 Action，触发 State 更新为“正在操作”状态，View 重新渲染
- 操作结束后，再送出一个 Action，触发 State 更新为“操作结束状态”，View 再一次重新渲染

## 官方文档阅读

The whole state of your app is stored in an object tree inside a single store. The only way to change the state tree is to emit an action, an object describing what happened. To specify how the actions transform the state tree, you write pure reducers.

- 应用的 state 储存在 object 中
- 改变 state 的唯一方式就是 emit 一个 action
- object 监听 action
- 通过纯函数 reducer 来控制 action 来改变 state 中的数据

reducer 的定义：it’s just a function that takes state and action as arguments, and returns the next state of the app.

接受 state 和 action 作为参数，返回下一个 state。

三大原则：

- The state of your whole application is stored in an object tree within a single store.
- The only way to change the state is to emit an action, an object describing what happened.（改变状态只能通过派发 action，通过一个对象去描述）
- To specify how the state tree is transformed by actions, you write pure reducers.（reducer 是纯函数）

### 与 React 相互配合使用

- Redux
  - combineReducers 将 reducer 合并
  - createStore 创建一个 Redux store 来以存放应用中所有的 state，将根 reducer 传入
  - Store
    - getState()
    - dispatch(action)
    - subscribe(listener)
    - replaceReducer(nextReducer)
- react-redux
  - Provider 是什么东西
  - connect 可以理解为将 state 和 dispatch 映射到 props 组件中
    - mapStateToProps?: (state, ownProps?) => Object
    - mapDispatchToProps?: Object | (dispatch, ownProps?) => Object
    - 其中 connect 可以用装饰器的方式来书写，简化流程
- redux-thunk 是一个 redux 中间件，用来处理异步，Redux 默认只处理同步任务
- reselect

容器组件(Container Components)和展示组件(Presentational Components)

### 函数签名记录

```js
// 关键调用
const store = createStore(reducer, applyMiddleware(thunkMiddleware));
```

```js
// createStore
function createStore(reducer, preloadedState, enhancer) {
  // 这里处理的是没有设定初始状态的情况，也就是第一个参数和第二个参数都传 function 的情况
  if (typeof preloadedState === "function" && typeof enhancer === "undefined") {
    // 此时第二个参数会被认为是 enhancer（中间件）
    enhancer = preloadedState;
    preloadedState = undefined;
  }
  // 当 enhancer 不为空时，便会将原来的 createStore 作为参数传入到 enhancer 中
  if (typeof enhancer !== "undefined") {
    return enhancer(createStore)(reducer, preloadedState);
  }
}
```

```js
// thunkMiddleware 就是 redux-thunk 导出的函数
({ dispatch, getState }) => (next) => (action) => {
  if (typeof action === "function") {
    return action(dispatch, getState, extraArgument);
  }

  return next(action);
};
```

```js
// middlewares 数组中的函数
[({ dispatch, getState }) =>(next) => (action) => {
  if (typeof action === "function") {
    return action(dispatch, getState, extraArgument);
  }

  return next(action);
}]
```

```js
// applyMiddlerware 会使用“...”运算符将入参收敛为一个数组
export default function applyMiddleware(...middlewares) {
  // 它返回的是一个接收 createStore 为入参的函数
  return (createStore) => (...args) => {
    // 首先调用 createStore，创建一个 store
    const store = createStore(...args);
    let dispatch = () => {
      throw new Error(
        `Dispatching while constructing your middleware is not allowed. ` +
          `Other middleware would not be applied to this dispatch.`
      );
    };

    // middlewareAPI 是中间件的入参
    const middlewareAPI = {
      getState: store.getState,
      dispatch: (...args) => dispatch(...args),
    };
    // 遍历中间件数组，调用每个中间件，并且传入 middlewareAPI 作为入参，得到目标函数数组 chain
    const chain = middlewares.map((middleware) => middleware(middlewareAPI));
    // 改写原有的 dispatch：将 chain 中的函数按照顺序“组合”起来，调用最终组合出来的函数，传入 dispatch 作为入参
    dispatch = compose(...chain)(store.dispatch);

    // 返回一个新的 store 对象，这个 store 对象的 dispatch 已经被改写过了
    return {
      ...store,
      dispatch,
    };
  };
}
```

按照代码执行顺序一点点看 `applyMiddleware(thunkMiddleware)`

返回一个高阶函数，高阶函数返回一个高阶函数

现在来看
