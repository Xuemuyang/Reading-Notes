# JS面试题

1.输出是什么

```javascript
function Foo() {
    var i = 0;
    return function() {
        console.log(i++);
    }
}

var f1 = Foo(),
    f2 = Foo();

f1();
f1();
f2();
//0 1 0
```

每一次函数调用都会创建一个独立的变量对象，函数内部的变量作为它的属性存在。两次调用`Foo()`时，内部的`i`是不同的变量。

2.输出是什么

```javascript
var myObject = {
    foo: "bar",
    func: function() {
    	var self = this;
    	console.log(this.foo);
    	console.log(self.foo);

    	(function() {
    	    console.log(this.foo);
    	    console.log(self.foo);
    	}());
    }
};
myObject.func(); // bar bar undefined bar
```

`this`是动态作用域，调用时绑定，作用域是词法作用域。
这个立即执行函数的调用方式为自执行，`this`指向全局，`self.foo`会在词法作用域中查找。

3.输出是什么

```javascript
function test() {
    var n = 4399;

    function add() {
        n++;
        console.log(n);
    }

    return {
        n: n,
        add: add
    }
}
var result = test();
var result2 = test();

result.add();
result.add();
console.log(result.n);
result2.add();
//4400 4401 4399 4400
```

`return`中的`add`函数是一个闭包，所以`console.log(result.n)`中的`add`方法与`result`中的`n`没有直接关系,等于是`test`中`add`函数的别名。

4.小算法题

给定一个长度小于`50`且包含字母和数字的任意字符串，要求按顺序取出当中的数字和英文字母，数字需要去重，重新排列后的字符串数字在前，字母在后。

样例输入

```js
'携程C2t0r1i8p2020校招'
```

样例输出

```js
'2018Ctrip'
```

```js
[...new Set([...str.match(/\d/g),...str.match(/[a-zA-Z]/g)])].join('')
```