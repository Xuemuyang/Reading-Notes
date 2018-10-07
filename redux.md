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
  text: text
});
```

每个 Action 都是一个对象，包含一个`actionType`属性(说明动作的类型)和 payload 即一些其他属性(用来传递数据)。

### Dispatcher

Dispatcher 的作用是将 Action 派发到 Store，可以将其看做是一个路由器，负责在 View 和 Store 之间建立 Action 的正确传递路线。

## Redux基础

### Action

Action 是把数据从应用传到 Store 的有效荷载，是 Store 数据的唯一来源。

约定action内必须使用一个字符串类型的`type`字段来表示将要执行的动作，多数情况下，`type`会被定义成字符串常量。建议使用调度的模块或文件来存放actionTypes。

```js
import { ADD_TODO, REMOVE_TODO } from '../actionTypes'
```

Action创建函数

```js
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}
```

### Reducer

Reducer是一个纯函数，接收旧的state和action，返回新的state

```js
(previousState, action) => newState
```

### Store

Redux应用只有一个单一的store

```js
import { createStore } from 'redux'
import todoApp from './reducers'
let store = createStore(todoApp)
```

### 数据流

严格的单向数据流是Redux架构的设计核心

1. 调用`store.dispatch(action)`
2. Redux store调用传入的reducer函数
3. 根reducer应该把多个子reducer输出合并成一个单一的state树
4. Redux store保存了根reducer返回的完整state树