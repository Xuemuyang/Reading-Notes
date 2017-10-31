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

#### jQuery对象和DOM对象的相互转化

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

```js
var $para = $('p');
alert($para.attr('title'));
```

### $()创建节点

```js
var $hehe = $('<li title="你好">你好</li>')
```

### 插入节点

+ append() 向匹配的元素内部追加内容
+ prepend()
+ after() 在每个匹配的元素之后插入内容
+ before()

还有一组方法

+ appendTo()
+ prependTo()
+ insertAfter()
+ insertBefore()

### 删除节点

1.`remove()`

remove之后还可以继续使用

```js
var $li = $('ul li:eq(1)').remove();
$li.appendTo('ul');
```

2.`detach()`
与`remove()`不同的是`detach()`方法所有绑定的事件、附加的数据都会保留下来

3.`empty()`
清空节点

### 复制节点

`clone()`方法

```js
$('ul li').click(function() {
    $(this).clone().appendTo('ul');
})
```

这样复制的新元素不具有任何行为，加入`true`参数复制的新元素同时复制元素中绑定的事件

```js
$('ul li').click(function() {
    $(this).clone().appendTo('ul');
})
```

### 替换节点

+ replaceWith()
+ replaceALl()

```js
$('p').replaceWith('<strong>hehe</strong>');
$('<strong>hehe</strong>').replaceAll('p');
```

### 包裹节点

+ wrapAll()
+ wrapInner()

### 属性操作

#### 获取属性和设置属性

`attr()`

```js
var $para = $('p');
var p_txt = $para.attr('title');

$('p').attr('title', 'hehe'); //设置属性
$('p').attr({'title': 'hehe', 'name': 'oh'}); //传入一个对象设置多个属性
```

#### 删除属性

`removeAttr()`

```js
$('p').removeAttr('title');
```

### 样式操作

#### 获取样式和设置样式

```js
var p_class = $('p').attr('class');
$('p').attr('class','high');
```

#### 追加样式

`addClass()`

中间用空格隔开一次添加多个样式

#### 移除样式

`removeClass()`

中间用空格隔开一次添加多个样式

#### 切换样式

`toggleClass()`如果类名存在则删除样式，不存在则添加样式

#### 判断是否含有某个样式

`hasClass()`

### 设置和获取HTML、文本和值

+ html()
+ text()
+ val()

### 遍历节点

+ `children()` 获取匹配元素的所有子元素，只有子元素，没有后代元素
+ `next()` 获取匹配元素后紧邻的同辈元素
+ `prev()` 获取匹配元素前面紧邻的同辈元素
+ `siblings()` 取得匹配元素前后所有的同辈元素
+ `parent()` 获得集合中每个元素匹配的父级元素，只返回一个


## Chap4. jQuery中的事件和动画

### jQuery中的事件

#### 加载DOM

`$(document).ready()`方法与`window.onload`方法有相似的功能，不同的是用jQuery方法在DOM完全就绪时就可以被调用，不需要所有关联文件都加载完毕。

有三种写法

1. `$(document).ready(function(){ //编写代码  })`
2. `$(function(){ //编写代码 })`
3. `$().ready(function(){ //编写代码 })`

#### 事件绑定
`bind( type [, data] , fn);`

三个参数
1. 事件类型，类型包括: blur、focus、load、resize、scroll、unload、click、dbclick、mousedown、mouseup、mouseover、mouseout、mouseenter、mouseleave、change、select、submit、keydown、keypress、keyup和error等
2. 可选参数，作为`event.data`属性值传递给事件
3. 事件处理函数

#### 合成事件

##### `hover()`

当光标移动到元素上时，会出发指定的第1个函数(enter)，当光标移出这个元素时，会触发指定的第2个函数(leave)

```js
$(function() {
    $('#hehe').hover(function() {
        xxx;
    },function() {
        xxx;
    });
});
```

##### `toggle()`

`toggle()`方法的语法结构为：
`toggle(fn1, fn2, ...fnN);`

`toggle()`用于模拟鼠标连续单击事件，第一次单击元素，触发指定的第一个函数，再次单击触发第二个函数，如果有更多函数，依次触发，直到最后一个。

`toggle()`方法还有一个作用：切换元素的可见状态。

```js
#('hehe').toggle();
```

#### 事件对象

阻止事件冒泡

```js
$('span').click(function(event) {
    xxx;
    event.stopPropagation();
});
```

同样阻止默认行为

`preventDefault()`

#### 事件对象的属性

+ `event.type`
+ `event.preventDefault()`
+ `event.stopPropagation()`
+ `event.target`
+ `event.relatedTarget`
+ `event.pageX`和`event.pageY` 获取光标相对于页面的x和y坐标，当没有使用jQuery时，Firefox中用的是`event.pageX/event.pageY`
+ `event.which` 点击事件中捕获到鼠标的左、中、右键，键盘事件中获取键盘的按键。
+ `event.metaKey` 

#### 移除事件

##### 移除按钮上以前注册的事件

`unbind([type],[data]);`

1. 如果没有参数，则删除所有绑定的事件
2. 如果提供了事件类型作为参数，则只删除该类型的绑定事件
3. 如果把在绑定时传递的处理函数作为第二个参数，只有这个特定的事件处理函数会被删除，如果这样，之前注册事件时需要命名

`one()`只调用一次，自动删除

#### 模拟操作

```js
$('#btn').trigger('click');
$('#btn').click(); //简写
```

##### 触发自定义操作

```js
$('#btn').bind('myClick', function() {
    $('#test').append('<p>hehe</p>');
});
```

##### 传递数据

```js
$('#btn').bind('myClick', function(event, msg1, msg2) {
    $('#test').append('<p>'+msg1+msg2+'</p>');
});

$('#btn').trigger('myClick', ['oh', 'hehe']);
```

##### 执行默认操作

`trigger()`触发事件后会执行浏览器默认操作。
`triggerHandler()`触发事件后会执行浏览器默认操作。

### jQuery中的动画

#### `show()`和`hide()`方法

```js
$('element').hide();
$('element').css('display', 'none'); //效果相同
```

可以加动画持续时间参数
+ slow == 600
+ normal == 400
+ fast == 200

还可以指定数字
```js
$('element').show(1000);
```

#### `fadeIn()`和`fadeOut()`

改变元素不透明度直至完全消失

#### `animate()`

`animate(params, speed, callback);`
+ params:包含属性样式属性及值的映射,比如{property1:"value1", property2:"value2",...}
+ speed:速度，可选
+ callback:动画完成时执行的函数，可选

##### 累加累减动画

```js
$('#hehe').click(function() {
    $(this).animate({ left:"+=500" }, 300);//在当前位置累加500px
})
```

##### 多重动画

1. 同时执行多个动画

```js
$('#hehe').click(function() {
    $(this).animate({left:"500px", height:"200px"}, 3000);
})
```

2. 按顺序执行多个动画

```js
$('#hehe').click(function() {
    $(this).animate({left:"500px"}, 3000);
    $(this).animate({height:"200px"}, 3000);
})
```

也可以采用链式写法

```js
$('#hehe').click(function() {
    $(this).animate({left:"500px"}, 3000)
        .animate({height:"200px"}, 3000);
})
```

#### 动画回调函数

存在动画队列，`css()`方法会立即执行，如果想用`css()`方法控制样式，将其写在动画回调函数里

```js
$('#hehe').click(function() {
    $(this).animate({left:"500px"}, 3000)
        .animate({height:"200px"}, 3000,function() {
            $(this).css("border", "5px solid blue");
        });
});
```

`callback`回调函数适合`jQuery`所有的动画效果方法

#### 停止动画和判断是否处于动画状态

##### 停止元素的动画

`stop()`方法

`stop([clearQueue], [goEnd]);`

两个参数都是`boolean`值

+ clearQueue表示清空未执行完的动画队列
+ gotoEnd代表是否直接将正在执行的动画跳转到末状态

##### 判断元素是否处于动画状态

```js
if(!$('#hehe').is(":animated")){ //判断元素是否处于动画状态
    //如果当前没有进行动画，则添加新动画
}
```

##### 延迟动画

`delay()`将队列中的函数延时执行

#### 其他动画方法

+ `toggle(speed, [callback])`
+ `slideToggle(speed, [easing], [callback])`
+ `fadeTo(speed, opacity, [callback])`
+ `fadeToggle(speed, [easing], [callback])`
