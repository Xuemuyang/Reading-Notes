# React

## JSX简介

`JSX`是`JavaScript`扩展语法，`React`推荐使用`JSX`来描述用户界面。

### JSX属性

可以使用引号来定义以字符串为值的属性：

```js
const element = <div tabIndex="0"></div>
```

也可以使用大括号来定义以`JavaScript`表达式为值的属性：

```js
const element = <img src={user.avatarUrl}></img>
```

这里的代码没有问题，

`JSX`会将引号中的内容识别为字符串而不是表达式。

### JSX嵌套

如果`JSX`标签是闭合式的，需要在结尾处用`/>`。

> jsx和原生html是有区别的，元素可以是自闭和也可以是`</ele>`这样闭合，最后jsx都会转换成合法的格式，如果你写一个空div，也可以是`<div />`

还有一些其他的点

`class`=>`className`,`for`=>`htmlFor`

### JSX防注入攻击

可以放心的在`JSX`中使用用户输入，`React DOM`在渲染之前会默认过滤所有传入的值，确保不会被注入攻击。

### JSX代表Objects

`Babel`会把`JSX`转换成一个名为`React.createElement()`的方法调用。

下面两种代码的作用是完全相同的:

```js
const element = (
    <h1 className="greeting">
        Hello, world!
    </h1>
);
```

```js
const element = React.createElement(
    'h1',
    {className: 'greeting'},
    'Hello, world!'
);
```

## 元素渲染

### 将元素渲染到DOM中

```html
<div id="root"></div>
```

```js
const element = <h1>Hello, world</h1>;
ReactDOM.render(
    element,
    document.getElementById('root')
);
```

### 更新元素渲染

`React`元素都是`immutable`不可变的，当元素被创建之后，是无法改变其内容或属性的，一个元素就好像是动画里的一帧，代表应用界面在某一时间点的样子。

### React只会更新必要的部分

`React DOM`首先会比较元素内容先后的不同，在渲染过程中只会更新改变的部分。

## 组件&Props

组件从概念上看就像是函数，可以接收任意的输入值（称之为"props"），并返回一个需要在页面上展示的`React`元素。

### 函数定义/类定义组件

定义一个组件最简单的方式是使用`JavaScript`函数：

```js
function Welcome(props) {
    return <h1>Hello, {props.name}</h1>
}
```

函数定义组件，传入`props`对象，返回一个`React`元素。

也可以使用`ES6 class`定义一个组件

```js
class Welcome extends React.Component {
    render() {
        return <h1>Hello, {this.props.name}</h1>
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
ReactDOM.render(
    element,
    document.getElementById('root')
);
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

ReactDOM.render(
    <App />,
    document.getElementById('root')
);
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
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

提取`Avatar`组件

```js
function Avatar(props) {
  return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
    />
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
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

### Props的只读性

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
  ReactDOM.render(
    <Clock date={new Date()} />,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

通过5个步骤将函数组件`Clock`转换为类

1. 创建一个名称扩展为`React.Componet`的ES6类
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
        <h2>It is{this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

`Clock`现在被定义为一个类而不是一个函数

使用类就允许我们使用其他特性，例如局部状态、生命周期钩子

### 为一个类添加局部状态

3个步骤将`date`从属性移动到状态中：

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
    this.state = {date: new Date()};
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
ReactDOM.render(
    <Clock />,
    document.getElementById('root')
);
```

结果如下：

```js
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
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

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

### 将生命周期方法添加到类中

在组件类上声明特殊的方法，当组件挂在或卸载时，运行一些代码：

```js
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {

  }

  componentWillUnmount() {

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
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
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

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

调用顺序：

1. 当`<Clock />`被传递给`ReactDOM.render()`时，`React`调用`Clock`组件的构造函数。 由于`Clock`需要显示当前时间，所以使用包含当前时间的对象来初始化`this.state`。我们稍后会更新此状态。

1. `React`然后调用`Clock`组件的`render()`方法。这是`React`了解屏幕上应该显示什么内容，然后`React`更新`DOM`以匹配`Clock`的渲染输出。

1. 当`Clock`的输出插入到`DOM`中时，`React`调用`componentDidMount()`生命周期钩子。在其中，`Clock`组件要求浏览器设置一个定时器，每秒钟调用一次`tick()`。

1. 浏览器每秒钟调用`tick()`方法。在其中，`Clock`组件通过使用包含当前时间的对象调用`setState()`来调度UI更新。 通过调用`setState()`，`React`知道状态已经改变，并再次调用`render()`方法来确定屏幕上应当显示什么。这一次，`render()`方法中的`this.state.date`将不同，所以渲染输出将包含更新的时间，并相应地更新`DOM`。

1. 一旦`Clock`组件被从`DOM`中移除，`React`会调用`componentWillUnmount()`这个钩子函数，定时器也就会被清除。

### 正确使用状态

三个需要注意的地方

#### 不要直接更新状态

此代码不会重新渲染组件：

```js
this.state.comment = 'Hello';
```

应当使用`setState()`

```js
this.setState({comment: 'Hello'});
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

自顶向下或单向数据流。任何状态始终由某些特定组件所有，并且从该状态导出的任何数据或UI只能影响树中下方的组件。

## 事件处理

`React`元素的事件处理和`DOM`元素很相似，有一点语法上的不同：

+ `React`事件绑定属性的命名采用驼峰式写法，而不是小写。
+ 如果采用`JSX`的语法你需要传入一个函数作为事件处理函数，而不是一个字符串(`DOM`元素的写法)

传统HTML

```html
<button onclick="activateLasers()">
  Activate Lasers
</button>
```

`React`中稍有不同

```js
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

另一个不同是不能使用返回`false`的方式阻止默认行为。必须明确的使用`preventDefault`。

传统HTML

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
        console.log('The link was clicked.');
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
    this.state = {isToggleOn: true};

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
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
);
```

除了`bind`之外，还有两种方式可以解决。

1.属性初始化器

```js
class LoggingButton extends React.Component {
     // This syntax ensures `this` is bound within handleClick.
    // Warning: this is *experimental* syntax.
    handleClick = () => {
        console.log('this is:', this);
    }

    render() {
        return (
            <button onClick={this.handleClick}>
            Click me
            </button>
        );
    }
}
```

2.箭头函数

```js
class LoggingButton extends React.Component {
    handleClick() {
        console.log('this is:', this);
    }

    render() {
        // This syntax ensures `this` is bound within handleClick
        return (
            <button onClick={(e) => this.handleClick(e)}>
            Click me
            </button>
        );
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
