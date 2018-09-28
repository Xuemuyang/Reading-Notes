# JavaScript函数式编程

函数是不同数值之间的特殊关系：每一个输入值返回且只返回一个输出值。

在函数式编程中，函数就是一个管道，从头进去一个值，另一头出来一个新的值，没有其他作用。

纯函数必须遵守以下约束。

+ 不得改写参数
+ 不能调用系统I/O的API
+ 不能调用Date.now()或者Math.random()等不纯的方法

函数的合成(compose)，一个值要经过多个函数，才能变成另外一个值，就可以把所有中间步骤合并成一个函数。

函数合成就像将管道连起来，让数据一口气从多个管道中穿过。

## curry

```js
const add = function(x) {
  return function(y) {
    return x + y
  }
}

const increment = add(1)
const addTen = add(10)

increment(2) // 3

addTen(2) // 12
```

定义了`add`函数，接受一个参数返回一个新的函数。调用`add`之后，返回的函数就通过闭包的方式记住了`add`的第一个参数。

通过传递一到两个参数调用函数，就能得到一个记住了这些参数的新函数。

## compose

```js
const compose = function(f, g) {
  return function(x) {
    return f(g(x))
  }
}
```

`f`和`g`都是函数，`x`是在他们之间通过"管道"传输的值。

函数的合成遵循结合律。

```js
compose(f, compose(g, h))
// 等同于
compose(compose(f, g), h)
// 等同于
compose(f, g, h)
```