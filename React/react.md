# React

## JSX 简介

`JSX`是`JavaScript`扩展语法，`React`推荐使用`JSX`来描述用户界面。

### JSX 属性

可以使用引号来定义以字符串为值的属性：

```js
const element = <div tabIndex="0" />;
```

也可以使用大括号来定义以`JavaScript`表达式为值的属性：

```js
const element = <img src={user.avatarUrl} />;
```

这里的代码没有问题，

`JSX`会将引号中的内容识别为字符串而不是表达式。

### JSX 嵌套

如果`JSX`标签是闭合式的，需要在结尾处用`/>`。

> jsx 和原生 html 是有区别的，元素可以是自闭和也可以是`</ele>`这样闭合，最后 jsx 都会转换成合法的格式，如果你写一个空 div，也可以是`<div />`

还有一些其他的点

`class`=>`className`,`for`=>`htmlFor`

### JSX 防注入攻击

可以放心的在`JSX`中使用用户输入，`React DOM`在渲染之前会默认过滤所有传入的值，确保不会被注入攻击。

### JSX 代表 Objects

`Babel`会把`JSX`转换成一个名为`React.createElement()`的方法调用。

下面两种代码的作用是完全相同的:

```js
const element = <h1 className="greeting">Hello, world!</h1>;
```

```js
const element = React.createElement(
  "h1",
  { className: "greeting" },
  "Hello, world!"
);
```

## 元素渲染

### 将元素渲染到 DOM 中

```html
<div id="root"></div>
```

```js
const element = <h1>Hello, world</h1>;
ReactDOM.render(element, document.getElementById("root"));
```

### 更新元素渲染

`React`元素都是`immutable`不可变的，当元素被创建之后，是无法改变其内容或属性的，一个元素就好像是动画里的一帧，代表应用界面在某一时间点的样子。

### React 只会更新必要的部分

`React DOM`首先会比较元素内容先后的不同，在渲染过程中只会更新改变的部分。

## 组件&Props

组件从概念上看就像是函数，可以接收任意的输入值（称之为"props"），并返回一个需要在页面上展示的`React`元素。

### 函数定义/类定义组件

定义一个组件最简单的方式是使用`JavaScript`函数：

```js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

函数定义组件，传入`props`对象，返回一个`React`元素。

也可以使用`ES6 class`定义一个组件

```js
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

上面两个组件在`React`中是相同的。

### 组件渲染

当`React`遇到的元素是用户自定义的组件，它会将`JSX`属性作为单个对象传递给该组件，这个对象称之为"props"。

```js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(element, document.getElementById("root"));
```

> 组件名称必须以大写字母开头。

### 组合组件

```js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}

ReactDOM.render(<App />, document.getElementById("root"));
```

组件的返回值只能有一个根元素。这也是我们要用一个`<div>`来包裹所有`<Welcome />`元素的原因。

### 提取组件

将组件切分为更小的组件。

`Comment`组件

```js
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img
          className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">{props.author.name}</div>
      </div>
      <div className="Comment-text">{props.text}</div>
      <div className="Comment-date">{formatDate(props.date)}</div>
    </div>
  );
}
```

提取`Avatar`组件

```js
function Avatar(props) {
  return (
    <img className="Avatar" src={props.user.avatarUrl} alt={props.user.name} />
  );
}
```

之后对`Comment`组件做一些小调整

```js
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <Avatar user={props.author} />
        <div className="UserInfo-name">{props.author.name}</div>
      </div>
      <div className="Comment-text">{props.text}</div>
      <div className="Comment-date">{formatDate(props.date)}</div>
    </div>
  );
}
```

### Props 的只读性

无论是使用函数或是类来声明一个组件，它决不能修改它自己的`props`。

```js
function sum(a, b) {
  return a + b;
}
```

类似于上面这种函数称为"纯函数"，没有改变自己的输入值。

非纯函数，会改变它自身的输入值：

```js
function withdraw(account, amount) {
  account.total -= amount;
}
```

`React`有一个非常严格的规则：

> 所有的`React`组件必须像纯函数那样使用它们的`props`。

## State&生命周期

### 将函数转换为类

```js
function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}

function tick() {
  ReactDOM.render(<Clock date={new Date()} />, document.getElementById("root"));
}

setInterval(tick, 1000);
```

通过 5 个步骤将函数组件`Clock`转换为类

1. 创建一个名称扩展为`React.Componet`的 ES6 类
1. 创建一个叫做`render()`的空方法
1. 将函数体移动到`render()`方法中
1. 在`render()`方法中，使用`this.props`替换`props`
1. 删除剩余的空函数声明

```js
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>
          It is
          {this.props.date.toLocaleTimeString()}.
        </h2>
      </div>
    );
  }
}
```

`Clock`现在被定义为一个类而不是一个函数

使用类就允许我们使用其他特性，例如局部状态、生命周期钩子

### 为一个类添加局部状态

3 个步骤将`date`从属性移动到状态中：

1.在`render()`方法中使用`this.state.date`替代`this.props.date`

```js
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

2.添加一个类构造函数来初始化`this.state`

```js
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = { date: new Date() };
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

类组件应始终使用`props`调用基础构造函数。

3.从`<Clock />`元素移除`date`属性：

```js
ReactDOM.render(<Clock />, document.getElementById("root"));
```

结果如下：

```js
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = { date: new Date() };
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(<Clock />, document.getElementById("root"));
```

### 将生命周期方法添加到类中

在组件类上声明特殊的方法，当组件挂在或卸载时，运行一些代码：

```js
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = { date: new Date() };
  }

  componentDidMount() {}

  componentWillUnmount() {}

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

这些方法被称作`生命周期钩子`。

当组件输出到`DOM`后会执行`compinentDidMount()`钩子。

```js
componentDidMount() {
    this.timerID = setInterval(
        () => this.tick(),
        1000
    );
}
```

如果需要存储不用于视觉输出的东西，则可以手动向类中添加其他字段。

在`componentWillUnmount()`生命周期钩子中写在计时器。

```js
componentWillUnmount() {
    clearInterval(this.timerID);
}
```

```js
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = { date: new Date() };
  }

  componentDidMount() {
    this.timerID = setInterval(() => this.tick(), 1000);
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    // 使用setState方法来调度UI更新
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(<Clock />, document.getElementById("root"));
```

调用顺序：

1. 当`<Clock />`被传递给`ReactDOM.render()`时，`React`调用`Clock`组件的构造函数。 由于`Clock`需要显示当前时间，所以使用包含当前时间的对象来初始化`this.state`。我们稍后会更新此状态。

1. `React`然后调用`Clock`组件的`render()`方法。这是`React`了解屏幕上应该显示什么内容，然后`React`更新`DOM`以匹配`Clock`的渲染输出。

1. 当`Clock`的输出插入到`DOM`中时，`React`调用`componentDidMount()`生命周期钩子。在其中，`Clock`组件要求浏览器设置一个定时器，每秒钟调用一次`tick()`。

1. 浏览器每秒钟调用`tick()`方法。在其中，`Clock`组件通过使用包含当前时间的对象调用`setState()`来调度 UI 更新。 通过调用`setState()`，`React`知道状态已经改变，并再次调用`render()`方法来确定屏幕上应当显示什么。这一次，`render()`方法中的`this.state.date`将不同，所以渲染输出将包含更新的时间，并相应地更新`DOM`。

1. 一旦`Clock`组件被从`DOM`中移除，`React`会调用`componentWillUnmount()`这个钩子函数，定时器也就会被清除。

### 正确使用状态

三个需要注意的地方

#### 不要直接更新状态

此代码不会重新渲染组件：

```js
this.state.comment = "Hello";
```

应当使用`setState()`

```js
this.setState({ comment: "Hello" });
```

构造函数式唯一能够初始化`this.state`的地方。

#### 状态更新可能是异步的

由于`this.props`和`this.state`可能是异步更新的，不应该靠他们的值来计算下一个状态。

```js
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}));
```

#### 状态更新合并

```js
constructor(props) {
    super(props);
    this.state = {
        posts: [],
        comments: []
    };
  }
```

调用`setState()`独立更新他们:

```js
componentDidMount() {
    fetchPosts().then(response => {
        this.setState({
        posts: response.posts
      });
    });

    fetchComments().then(response => {
        this.setState({
            comments: response.comments
        });
    });
  }
```

这里的合并是浅合并，也就是说`this.setState({comments})`完整保留了`this.state.posts`，但完全替换了`this.state.comments`。

### 数据自顶向下流动

自顶向下或单向数据流。任何状态始终由某些特定组件所有，并且从该状态导出的任何数据或 UI 只能影响树中下方的组件。

## 事件处理

`React`元素的事件处理和`DOM`元素很相似，有一点语法上的不同：

- `React`事件绑定属性的命名采用驼峰式写法，而不是小写。
- 如果采用`JSX`的语法你需要传入一个函数作为事件处理函数，而不是一个字符串(`DOM`元素的写法)

传统 HTML

```html
<button onclick="activateLasers()">
  Activate Lasers
</button>
```

`React`中稍有不同

```js
<button onClick={activateLasers}>Activate Lasers</button>
```

另一个不同是不能使用返回`false`的方式阻止默认行为。必须明确的使用`preventDefault`。

传统 HTML

```html
<a href="#" onclick="console.log('The link was clicked.'); return false">
    Click me
</a>
```

`React`中

```js
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log("The link was clicked.");
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}
```

这个`Toggle`组件渲染一个让用户切换开关状态的按钮：

```js
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = { isToggleOn: true };

    // This binding is necessary to make `this` work in the callback
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? "ON" : "OFF"}
      </button>
    );
  }
}

ReactDOM.render(<Toggle />, document.getElementById("root"));
```

除了`bind`之外，还有两种方式可以解决。

1.属性初始化器

```js
class LoggingButton extends React.Component {
  // This syntax ensures `this` is bound within handleClick.
  // Warning: this is *experimental* syntax.
  handleClick = () => {
    console.log("this is:", this);
  };

  render() {
    return <button onClick={this.handleClick}>Click me</button>;
  }
}
```

2.箭头函数

```js
class LoggingButton extends React.Component {
  handleClick() {
    console.log("this is:", this);
  }

  render() {
    // This syntax ensures `this` is bound within handleClick
    return <button onClick={e => this.handleClick(e)}>Click me</button>;
  }
}
```

### 向事件处理程序传递参数

```js
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

两种方式是等价的，分别通过`arrow functions`和`Function.prototype.bind`来为特定事件添加事件处理程序。

通过箭头函数的方式，事件对象必须被显式的进行传递，通过`bind`的方式，事件对象以及更多的参数将会被隐式的进行传递。

## 条件渲染

## the-road-to-learn-react

### Introduction

这是 create-react-app 生成的代码，理解 component,instance 和 element 之间的关系至关重要

```js
import React, { Component } from "react";
import logo from "./logo.svg";
import "./App.css";

class App extends Component {
  render() {
    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h1 className="App-title">Welcome to React</h1>
        </header>
        <p className="App-intro">
          To get started, edit <code>src/App.js</code> and save to reload.
        </p>
      </div>
    );
  }
}

export default App;
```

App 是一个 React 组件，实例化之后使用，通过`render()`方法返回元素(element)

JSX 语法允许将 HTML 与 JavaScript 混合使用

JSX 中使用`{}`包裹 JS 表达式

#### ReactDOM

```js
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./App";

ReactDOM.render(<App />, document.getElementById("root"));
```

在一个纯 React 应用中，只需使用一次`render()`

`render`接收两个参数，第一个参数是需要被渲染的 JSX，第二个参数是 React 的挂载点，挂载之后会将 index.html 中挂载点中的元素全部替换

#### 模块热更新

在`index.js`中加入下面这段模块热更新代码

```js
if (module.hot) {
  module.hot.accept();
}
```

#### 复杂的 JSX

React 可以借助 key 这个辅助属性提高性能

需要给 key 这个属性一个稳定的标识符，索引往往是不稳定的

ES6 的箭头函数允许省略 return 表达式

```js
{
  list.map(item => (
    <div key={item.objctId}>
      <span>
        <a href={item.url}>{item.title}</a>
      </span>
      <span>{item.author}</span>
    </div>
  ));
}
```

ES6 的 class

```js
class Developer {
  constructor(firstname, lastname) {
    this.firstname = firstname;
    this.lastname = lastname;
  }

  getName() {
    return `${this.firstname} ${this.lastname}`;
  }
}
```

```js
const robin = new Developer("Robin", "Wieruch");
console.log(robin.getName());
// output: Robin Wieruch
```

### Basics in React

每当我们改变了组件的 state，`render()`方法都会执行

```js
class App extends Component {
  constructor(props) {
    super(props);
    this.state = {
      hehe: "hehe"
    };
  }
}
```

元素中一行有多个属性看起来会比较混乱，推荐使用多行格式来书写以保持可读性

```js
class App extends Component {
  constructor(props) {
    super(props);

    this.state = {
      hehe,
    };

    this.onDismiss = this.onDismiss.bind(this)
  }

  onDismiss(id) {
    const updatedList = this.state.list.filter(item => item.objectID !== id);
    this.setState({ list: updatedList });
  }

  render() {
    ...
  }
}
```

使用类方法`setState()`来更新组件 state

binding 操作是必要的，方法不会自动绑定到类的实例上

构造函数的目的只是实例化一个类以及所有属性，业务逻辑应该写在构造函数之外，这就是为什么需要`this.onDismiss = this.onDismiss.bind(this)`

类方法可以通过箭头函数做到自动地绑定

#### 事件处理

传递参数时需要涉及到高阶函数(higher-order functions)，使用一个函数返回另一个函数

```jsx
<button onClick={() => this.onDismiss(item.objectId)} type="button">
  Dismiss
</button>
```

传给事件处理器的一定是一个函数

像下面这样的写法，testMethod 函数会在浏览器打开程序的时候立即执行

```js
render() {
  return (
    <div className="App">
      <h2 onClick={this.testMethod('hehe')}>{this.state.hehe}</h2>
    </div>
  );
}
```

这是搜索的一个例子

```js
render() {
  return (
    <div className="App">
      <form>
        <input
          type="text"
          onChange={this.onSearchChange}
        />
      </form>
      {this.state.list.filter(isSearched(this.state.searchTerm)).map(item =>
        ...
      )}
    </div>
  );
}
```

#### Controlled Components(受控组件)

比如表单元素`<input>`，它的值可以通过 value 元素来改变，在 React 中这被称为不受控组件。

```jsx
<input type="text" value={searchTerm} onChange={this.onSearchChange} />
```

这样处理之后，就形成了自包含的单向数据流

#### Functional Stateless Components(函数式无状态组件)

这类组件可以可以理解为函数，输入 props，输出 JSX 组件实例，这类组件中没有`this.state`和`this.setState()`因为他们没有`this`对象，也没有生命周期方法，但是可以有`constructor()`和`render()`

### Lifecycle

在挂载过程中有四个生命周期方法，调用顺序如下

- `constructor()`
- `componentWillMount()`
- `render()`
- `componentDidMount()`

当组件状态或属性改变有五个  生命周期用于组件更新

- `componentWillReceiveProps()`
- `shouldComponentUpdate()`
- `componentWillUpdate()`
- `render()`
- `componentDidUpdate()`

组件卸载

- `componentWillUnmount()`

React 允许组件通过返回`null`来不渲染任何东西

React 拥护不可变的数据结构，相比直接改变对象的属性，更好的做法是创建一个新的对象

对象的扩展运算符可以用来替代`Object.assign()`

使用 PropTypes 来进行参数类型检查

## React Router

### Basic Components

三种组件

- router
- router match
- navigation

1.Routers

React提供了`<BrowserRouter>`和`<HashRouter>`，总的来说如果做静态文件服务器的话推荐使用`<HashRouter>`

2.Route Matching

将当前的路径与`<Route>`的path属性比较，匹配就将内容render，否则render null，一个没有path属性的`<Router>`总是会被匹配。
