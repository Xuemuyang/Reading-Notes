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

## 废话

Redux使用场景

- 用户的使用方式复杂
- 不同身份的用户有不同的使用方式（比如普通用户和管理员）
- 多个用户之间可以协作
- 与服务器大量交互，或者使用了WebSocket
- View要从多个来源获取数据

从组件角度的使用场景

- 某个组件的状态，需要共享
- 某个状态需要在任何地方都可以拿到
- 一个组件需要改变全局状态
- 一个组件需要改变另一个组件的状态

设计思想

1. Web应用是一个状态机，视图与状态是一一对应的。
2. 所有的状态，保存在一个对象里面。

## 基础

### Store

Store是保存数据的地方，Redux应用只有一个单一的store。

```js
import { createStore } from 'redux'
import todoApp from './reducers'
let store = createStore(todoApp)
```

Store提供了三个方法

- `store.getState()`
- `store.dispatch()`
- `store.subscribe()`

Store允许`store.subscribe`设置监听函数，一旦State发生变化，就自动执行函数。将View的更新函数放入其中就会实现View的自动渲染。

应用状态默认值可以在`createStore`的第二个参数中传入，也可以在`Reducer`的默认初始值给出，看场景选用。

### State

`Store`对象的快照叫做State。

当前时刻的State可以用`store.getState()`拿到。

```js
import { createStore } from 'redux'
import todoApp from './reducers'
let store = createStore(todoApp)

const state = store.getState()
```

Redux规定一个State对应一个View。

### Action

Action 是把数据从应用传到 Store 的有效荷载，是 Store 数据的唯一来源。

约定action内必须使用一个字符串类型的`type`字段来表示将要执行的动作，多数情况下，`type`会被定义成字符串常量。建议使用调度的模块或文件来存放actionTypes。

```js
import { ADD_TODO, REMOVE_TODO } from '../actionTypes'
```

Action创建函数(Action Creator)

```js
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}

const action = addTodo('Learn Redux')
```

通过调用`store.dispatch()`将Action发出去。

### Reducer

Store收到Action之后，必须给出一个新的State，这样View才会发生变化。这种State的计算过程就叫做Reducer。

Reducer是一个纯函数，接收旧的state和action，返回新的state。

纯函数可以保证同样的State，必定得到同样的View。

```js
(previousState, action) => newState
```

Reducer不需要手动调用，`store.dispatch`方法触发Reducer自动执行。

```js
import { createStore } from 'redux'
let store = createStore(reducer)
```

每当`store.dispatch`发送过来一个新的Action，就会自动调用Reducer，得到新的State。

#### Reducer的拆分

使用Redux提供的`combineReducers`方法，将子Reducer函数合成一个大的Reducer。

### 数据流

严格的单向数据流是Redux架构的设计核心

![redux-flow](./images/Redux/redux-flow.jpg)

1. 调用`store.dispatch(action)`，发出Action
1. Redux store自动调用Reducer，并传入当前的State和收到的Action，Reducer返回新的State
1. State一旦有变化，Store就会调用监听函数
1. 根Reducer应该把多个子Reducer输出合并成一个单一的State树
1. Redux store保存了根Reducer返回的完整State树

## 中间件和异步操作

异步操作思路

- 操作开始时，送出一个Action，触发State更新为“正在操作”状态，View重新渲染
- 操作结束后，再送出一个Action，触发State更新为“操作结束状态”，View再一次重新渲染
