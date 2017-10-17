## Chap1. 认识jQuery

`Prototype`、`Dojo`、`jQuery`06年、`Ext JS`07年

`Ajax`(Asynchronous JavaScript And XML)

优点
+ 强大选择器
+ 出色的DOM操作
+ 可靠的事件处理
+ 完善的兼容性
+ 链式操作

`John Resig`创建于2006年1月。

### jQuery对象和DOM对象

`jQuery`对象就是通过`jQuery`包装`DOM`对象后产生的对象。

`jQuery`对象无法使用`DOM`对象的任何方法，`DOM`对象也无法使用`jQuery`里的方法。

#### ### jQuery对象和DOM对象的相互转化

1. `jQuery`对象转成`DOM`对象

`jQuery`提供了两种方法，[index]和get(index)

```js
var $cr = $('#cr'); //jQuery对象
var cr = $cr[0]; //DOM对象
```

```js
var $cr = $('#cr'); //jQuery对象
var cr = $cr.get(0); //DOM对象
```

2. `DOM`对象转成`jQuery`对象

```js
var cr = document.getElementById('cr'); //DOM对象
var $cr = $(cr); //jQuery对象
```

---
**`$()`函数**

`$()`这是`jQuery`的一个函数，也是最核心最基本的函数

+ 功能一：传入一个选择器字符串，获得这个选择器对应的dom内容，保存在[]中，也就是俗称的jQuery对象。例如:

```js
`$('div > li')`
```

+ 功能二：传入一个匿名函数，例如:

```js
`$(function(){})`//这个匿名函数在网页载入完成后开始执行 
```

+ 功能三：将`JavaScript`对象包装成为`jQuery对象`。例如：

```js
$(this)
$({a:1,b:2,c:3})
$(document.getElementById('idstr'))
```

## Chap2. jQuery选择器

```js
document.getElementById('#tt').style.color='red';
```
如果网页中没有id为"tt"的元素，那么浏览器会报错

```js
$('#tt').css('color',red);
```

`$('#tt')`获取的永远是对象，即使网页上没有此元素。因此当要用`jQuery`检查某个元素在网页上是否存在时，需要根据获取到的元素长度来判断。

```js
if ( $('#tt') ) {
    //do something
} // 错误！！！

if ( $('#tt').length > 0 ) {
    //do something
}
```

### 选择器

选择器|方法
---|---
`$('.one + div');`|`$(.one).next('div');`
`$('#prev~div');`|`$('#prev').nextAll('div');`

jQuery方法
+ `show()` 显示隐藏的匹配元素
+ `css(name,value)` 给元素设置样式
+ `text(string)` 设置所有匹配元素的文本内容
+ `filter(expr)` 筛选出与指定表达式匹配的元素集合，对自身集合元素进行筛选
+ `find()` 在元素内寻找匹配元素
+ `addClass(class)` 为匹配的元素添加指定的类名

## Chap3. jQuery中的DOM操作

