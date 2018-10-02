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

Store是保存数据的地方，一个容器，

### Action

Action 是把数据从应用传到 Store 的有效荷载，是 Store 数据的唯一来源。

### Reducer

reducer 就是一个纯函数，接收旧的 state 和 action，返回新的 state。

```js
(previousState, action) => newState
```