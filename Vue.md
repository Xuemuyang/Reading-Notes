# Vue

将数据放在data中，双向绑定，相互影响;

核心思想: 数据驱动，jQuery是结构驱动

目录结构
+ package.json中存放依赖信息
+ config保存一些项目初始化配置
+ build里面保存一些webpack的初始化配置
+ main.js是入口js文件

router
> 根据页面当前url来判断router-view中渲染什么组件

node端之所以能识别.vue文件，是因为前面说的webpack在编译时将.vue文件中的html，js，css都抽出来合成新的单独的文件。

使用锚点路由可以做到不用刷新页面

router中配置的name属性相当于一个备注

script中加入命名标签，打包程序会加上命名空间，确保样式之类不会冲突

props是专门用来暴露组件的属性接口的

那就是页面加载数据时，原始代码会闪现一下。

首先加入一段CSS代码

```html
<style type="text/css">  
    [v-cloak] {  
        display: none;  
    }  
</style>  
```

直接在vue挂载点上加入`v-cloak`指令
```html
<div id="app" v-cloak>  
    <h1>{{message}}</h1>  
    <h1>{{name}}</h1>  
</div> 
```


## 基本指令

声明式渲染
+ `{{}}`也叫`v-text` 文本渲染
+ `v-bind` 属性绑定 :

条件与循环
+ `v-if` DOM不渲染
+ `v-show` 通过display控制隐藏
+ `v-for` 列表渲染

处理用户输入
+ `v-on` 事件绑定 @
+ `v-model` 表单控件使用

### 1. 声明式渲染

#### `{{}}`

采用简洁的模板语法来声明式的将数据渲染进`DOM`

```html
<div id="app">
  {{ message }}
</div>
```

```javascript
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```

#### `v-bind`

属性绑定

```html
<div id="app-2">
  <span v-bind:title="message">
    鼠标悬停几秒钟查看此处动态绑定的提示信息！
  </span>
</div>
```

```javascript
var app2 = new Vue({
  el: '#app-2',
  data: {
    message: '页面加载于 ' + new Date().toLocaleString()
  }
})
```

`v-bind:title="message"`可以简写为`:title="message"`

`<div :f="{ active: isActive }"><div>`
通过`isActive`的值来绑定`div`的`active`属性

### 2. 条件与循环

#### `v-if`

```html
<div id="app-3">
  <p v-if="seen">现在你看到我了</p>
</div>
```

```javascript
var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
```

`v-if`控制的是标签不渲染
`v-show`是设置标签`display:none`

#### `v-for`

```html
<div id="app-4">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
```

```javascript
var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: '学习 JavaScript' },
      { text: '学习 Vue' },
      { text: '整个牛项目' }
    ]
  }
})
```

`app4.todos.push({ text: '新项目' })`会添加新项目，访问`todos`，直接`app4.todos`即可。

### 3. 处理用户输入

#### `v-on`

`v-on`用于监听DOM事件

```html
<div id="app-5">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">逆转消息</button>
</div>
```

```javascript
var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
    this.message = this.message.split('').reverse().join('')
    }
  }
})
```

`v-on:click`可以简写为`@click`

#### `v-model`

用来处理表单控件

```html
<div id="app-6">
  <p>{{ message }}</p>
  <input v-model="message">
</div>
```

```javascript
var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
```

## Vue实例

生命周期

![](http://ovyn493ey.bkt.clouddn.com/lifecycle.png)

## 模板语法

通过使用`v-once`指令，你也能执行一次性地插值，当数据改变时，插值处的内容不会更新。但请留心这会影响到该节点上所有的数据绑定：

```html
<span v-once>这个将不会改变: {{ msg }}</span>
```

### 使用JavaScript表达式

迄今为止，在我们的模板中，我们一直都只绑定简单的属性键值。但实际上，对于所有的数据绑定，Vue.js 都提供了完全的 JavaScript 表达式支持。

```html
{{ number + 1 }}
{{ ok ? 'YES' : 'NO' }}
{{ message.split('').reverse().join('') }}
<div v-bind:id="'list-' + id"></div>
```

这些表达式会在所属 Vue 实例的数据作用域下作为 JavaScript 被解析。有个限制就是，每个绑定都只能包含单个表达式，所以下面的例子都不会生效。

```html
<!-- 这是语句，不是表达式 -->
{{ var a = 1 }}
<!-- 流控制也不会生效，请使用三元表达式 -->
{{ if (ok) { return message } }}
```

> 模板表达式都被放在沙盒中，只能访问全局变量的一个白名单，如`Math`和`Date`。你不应该在模板表达式中试图访问用户定义的全局变量。

### `v-bind` 缩写

``` html
<!-- 完整语法 -->
<a v-bind:href="url"></a>
<!-- 缩写 -->
<a :href="url"></a>
```

### `v-on` 缩写

```html
<!-- 完整语法 -->
<a v-on:click="doSomething"></a>
<!-- 缩写 -->
<a @click="doSomething"></a>
```

## 计算属性

对于任何复杂逻辑都应该使用**计算属性**

```html
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>ted reversed message: "{{ reversedMessage }}"</p>
</div>
```

```javascript
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // a computed getter
    reversedMessage: function () {
      // `this` points to the vm instance
      return this.message.split('').reverse().join('')
    }
  }
})
```

### 计算属性缓存

在表达式中调用方法同样可以达到目的

```html
<p>Reversed message: "{{ reversedMessage() }}"</p>
```

```javascript
// in component
methods: {
  reversedMessage: function () {
    return this.message.split('').reverse().join('')
  }
}
```

不同的是计算属性是基于它们的依赖关系进行缓存，只要依赖关系没有发生变化，多次访问会立即返回结果，不必再次执行函数。

### `watch`例子

```html
<div id="watch-example">
  <p>
    Ask a yes/no question:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>
```

### 计算属性的`setter`

计算属性默认只有`getter`,也可以添加`setter`

```js
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```

现在再运行`vm.fullName = 'John Doe'`时，`setter`会被调用，`vm.firstName`和`vm.lastName`也会相应地被更新。

```html
<!-- Since there is already a rich ecosystem of ajax libraries    -->
<!-- and collections of general-purpose utility methods, Vue core -->
<!-- is able to remain small by not reinventing them. This also   -->
<!-- gives you the freedom to just use what you're familiar with. -->
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
<script>
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: 'I cannot give you an answer until you ask a question!'
  },
  watch: {
    // 如果 question 发生改变，这个函数就会运行
    question: function (newQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.getAnswer()
    }
  },
  methods: {
    // _.debounce 是一个通过 lodash 限制操作频率的函数。
    // 在这个例子中，我们希望限制访问 yesno.wtf/api 的频率
    // ajax 请求直到用户输入完毕才会发出
    // 学习更多关于 _.debounce function (and its cousin
    // _.throttle)，参考：https://lodash.com/docs#debounce
    getAnswer: _.debounce(
      function () {
        if (this.question.indexOf('?') === -1) {
          this.answer = 'Questions usually contain a question mark. ;-)'
          return
        }
        this.answer = 'Thinking...'
        var vm = this
        axios.get('https://yesno.wtf/api')
          .then(function (response) {
            vm.answer = _.capitalize(response.data.answer)
          })
          .catch(function (error) {
            vm.answer = 'Error! Could not reach the API. ' + error
          })
      },
      // 这是我们为用户停止输入等待的毫秒数
      500
    )
  }
})
</script>
```

## Class与Style绑定

### 绑定HTML Class

#### 对象语法

我们可以传给`v-bind:class`一个对象，以动态地切换class：

```html
<div v-bind:class="{ active: isActive }"></div>
```

上面的语法表示`classactive`的更新将取决于数据属性 `isActive`是否为真值。

结合计算属性一起使用

```html
<div v-bind:class="classObject"></div>
```

```javascript
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      'active': this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

#### 数组语法

把一个数组传给`v-bind:class`，以应用一个class列表：

```html
<div v-bind:class="[activeClass, errorClass]"></div>
```

```javascript
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

渲染为：

```html
<div class="active text-danger"></div>
```

与对象语法结合使用

```html
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

## 条件渲染

```html
<div v-if="Math.random() > 0.5">
  Now you see me
</div>
<div v-else>
  Now you don't
</div>
```

`v-else`元素必须紧跟在`v-if`或者`v-else-if`元素的后面——否则它将不会被识别。

v-else-if，顾名思义，充当 v-if 的“else-if 块”，可以连续使用：

```html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

类似于`v-else`，`v-else-if`也必须紧跟在带`v-if`或者`v-else-if`的元素之后。

## 列表渲染

变异方法

Vue包含一组观察数组的变异方法，所以它们也将会触发视图更新。这些方法如下：

```js
push()
pop()
shift()
unshift()
splice()
sort()
reverse()
```

使用这些方法对数组进行操作

### `v-for` with `v-if`

当它们处于同一节点，`v-for`的优先级比`v-if`更高，这意味着`v-if`将分别重复运行于每个`v-for`循环中。当你想为仅有的一些项渲染节点时，这种优先级的机制会十分有用，如下：

```html
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
```

上面的代码只传递了未complete的todos。

## 事件处理

`v-on`指令监听DOM事件来触发一些JavaScript代码

```html
<div id="example-1">
  <button v-on:click="counter += 1">增加 1</button>
  <p>这个按钮被点击了 {{ counter }} 次。</p>
</div>
```

```javascript
var example1 = new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
})
```

### 事件修饰符

在事件处理程序中调用`event.preventDefault()`或 `event.stopPropagation()`是非常常见的需求。尽管我们可以在`methods`中轻松实现这点，但更好的方式是：`methods`只有纯粹的数据逻辑，而不是去处理DOM事件细节。
为了解决这个问题，Vue.js为`v-on`提供了事件修饰符。通过由点 (.) 表示的指令后缀来调用修饰符。

+ .stop
+ .prevent
+ .capture
+ .self
+ .once

```html
<!-- 阻止单击事件冒泡 -->
<a v-on:click.stop="doThis"></a>
<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>
<!-- 修饰符可以串联 -->
<a v-on:click.stop.prevent="doThat"></a>
<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>
<!-- 添加事件侦听器时使用事件捕获模式 -->
<div v-on:click.capture="doThis">...</div>
<!-- 只当事件在该元素本身 (比如不是子元素) 触发时触发回调 -->
<div v-on:click.self="doThat">...</div>
<!-- 点击事件将只会触发一次 -->
<a v-on:click.once="doThis"></a>
```

