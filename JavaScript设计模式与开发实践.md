# JavaScript设计模式与开发实践

## 前言

"模式"最早诞生于建筑学。哈佛大学建筑学博士Christopher Alexander和他的研究团队花了约20年的时间，研究了为解决同一个问题而设计出的不同建筑结构，从中发现了那些高质量设计中的相似性，并且用"模式"来指代这种相似性。

通俗一点来说，设计模式是在某种场合下对某个问题的一种解决方案。

"好的设计"并不是某人发明的，而是一直存在的，大家会凭经验一直使用，"模式"不过是给"好的设计"起个名字。

设计模式的作用是让人们写出可复用和可维护性高的程序。

所有设计模式的实现都遵循"找出程序中变化的地方，将其封装起来"。

## 第一部分 基础知识

### 第1章 面向对象的JavaScript

#### 动态类型语言和鸭子类型

编程语言按照数据类型大体可以分为两类

+ 静态类型语言(编译时确定类型)
+ 动态类型语言(运行时确定类型)

> 鸭子类型(duck typing)，如果它走起路来像鸭子，叫起来也是鸭子，那么它就是鸭子。

在动态类型语言的面相对象设计中，鸭子类型的概念至关重要。我们可以轻松地实现"面相接口编程"。例如一个对象若有`push`和`pop`方法，它就可以被当做栈来使用。

#### 多态

JavaScript对象的多态性是与生俱来的，某一种动物能否发出叫声，只取决于它有没有`makeSound`方法，而不取决于它是否是某种类型的对象。

> 多态最根本的作用就是通过吧过程化的条件分支语句转化为对象的多态性，从而消除这些条件分支语句。

一个例子很好的诠释

电影拍摄现场，导演喊出"action"，主角开始背台词，灯光师傅打灯，群众演员倒地。得到同一个消息时，每个对象都知道自己应该做什么。

每个对象该做什么，事先已经是该对象的一个方法，被安装在对象的内部。

用代码举个🌰

一个地图应用，我们选择google的API

```js
const googleMap = {
  show() {
    console.log('开始渲染谷歌地图')
  }
}

const renderMap = () => {
  googleMap.show()
}

renderMap()
```

后来由于某些原因，我们需要把google换成百度，于是代码变成了这样:

```js
const googleMap = {
  show() {
    console.log('开始渲染谷歌地图')
  }
}

const baiduMap = {
  show() {
    console.log('开始渲染百度地图')
  }
}

const renderMap = type => {
  if (type === 'google') {
    googleMap.show()
  } else if (type === 'baidu') {
    baiduMap.show()
  }
}

renderMap('google')
renderMap('baidu')
```

一旦需要将其换成高德地图，必须要改动renderMap函数，继续堆砌条件分支语句。

将程序中相同的部分抽象出来:

```js
const renderMap = map => {
  if (map.show instanceof Function) {
    map.show()
  }
}

renderMap(googleMap)
renderMap(baiduMap)
```

#### 封装

#### 原型模式和基于原型继承的JavaScript对象系统

原型模式的实现关键，是语言本身是否提供了`clone`的方法。

```js
Object.create = Object.create || function( obj ){
  const F = function(){}
  F.prototype = obj
  return new F()
}
```

基于原型链的委托机制就是原型继承的本质。

原型编程范型至少包括以下基本规则:

+ 所有的数据都是对象
+ 要得到一个对象，不是通过实例化类，而是找到一个对象作为原型并克隆它
+ 对象会记住它的原型
+ 如果对象无法响应某个请求，它会把这个请求委托给它自己的原型

##### JavaScript中的原型继承

要得到一个对象，不是通过实例化类，而是找到一个对象作为原型并克隆它。

JavaScript个对象提供了一个名为`__proto__`的隐藏属性，某个对象的`__proto__`属性默认会指向它的构造器的原型对象，即{Construcrot}.prototype。在一些浏览器上`__proto__`属性被公开出来。

```js
const a = new Object()
console.log(a.__proto__ === Object.prototype)
```

### 第2章 this、call和apply

#### this

`this`的指向大致分为以下四种:

+ 作为对象的方法调用
+ 作为普通函数调用
+ 构造器调用
+ Function.prototype.call或Function.prototype.apply调用

当函数作为对象的方法被调用时，`this`指向该对象

函数不作为对象的属性被调用时，`this`总是指向全局对象

当用`new`运算符调用函数时，该函数总会返回一个对象，通常情况下，构造器里的`this`就指向返回的这个对象:

```js
let MyClass = function() {
  this.name = 'hehe'
}

let obj = new MyClass()
alert(obj.name) // output: hehe
```

如果构造器显式返回了一个object类型的对象，那么此次运算结果最终会返回这个对象，而不是之前期待的`this`。

```js
let MyClass = function() {
  this.name = 'hehe'
  return {
    name: 'haha'
  }
}
var obj = new MyClass()
alert ( obj.name ) // 输出:haha
```

#### call和apply

能够熟练使用`call`和`apply`是真正成为JavaScript程序员的重要一步。

##### call和apply的用途

1.改变`this`指向

```js
document.getElementById = (function(func){
  return function() {
    return func.apply(document, arguments)
  }
})(document.getElementById)
var getId = document.getElementById
var div = getId('div')
alert(div.id)
```

2.Function.prototype.bind
3.借用其他对象的方法

### 第3章 闭包和高阶函数

#### 闭包

一个🌰

```js
const Type = {};

for (let i = 0, type; type = ['String', 'Array', 'Number'][i++];) {
  Type['is' + type] = obj => {
    return Object.prototype.toString.call(obj) === `[object ${type}]`
  }
};

Type.isArray([])
Type.isString('str')
```

##### 封装变量

闭包可以将一些不需要暴露在全局的变量封装成"私有变量"。

```js
const mult = (...args) => {
  let val  = 1
  for (let i = 0; i < args.length; i++) {
    val *= args[i]
  }
  return val
}
```

加入缓存来提高这个函数的性能

```js
const cache = {}

const mult = (...args) => {
  let key = args.join(',')
  if (cache[key]) {
    return cache[key]
  }
  let val = 1
  for (let i = 0; i < args.length; i++) {
    val *= args[i]
  }
  return cache[key] = val
}
```

`cache`这个变量仅仅在`mult`函数中被使用，将其封闭在`mult`函数内部。

```js
const mult = () => {
  const cache = {}
  return function (...args) {
    let key = args.join(',')
    if (key in cache) {
      return cache[key]
    }
    let val = 1
    for (let i = 0; i < args.length; i++) {
      val *= args[i]
    }
    return cache[key] = val
  }
}
```

提炼函数是代码重构中的常见技巧，如果在一个大函数中有一些代码块能够独立出来，常常把这些代码块封装在独立的小函数里面。独立出来的小函数有助于代码复用，如果这些小函数有一个良好的命名，它们本身也起到了注释的作用。

```js
const mult = () => {
  const cache = {}
  const calculate = (...args) => {
    let val = 1
    for (let i = 0; i < args.length; i++) {
      val *= args[i]
    }
    return val
  }
  return function (...args) {
    let key = args.join(',')
    if (key in cache) {
      return cache[key]
    }
    return cache[key] = calculate.apply(null, args)
  }
}
```

#### 高阶函数