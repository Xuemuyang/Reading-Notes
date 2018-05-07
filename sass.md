# Sass快速入门

## 使用变量

`sass`为`css`引入变量，可以把反复使用的`css`属性值定义成变量，通过变量名来引用它们。

### 变量声明

基本格式如下

```css
$basic-border: 1px solid black;
```

存在块级作用域，在块中定义的变量只能在块中使用

```css
$nav-color: #F90;
nav {
  $width: 100px;
  width: $width;
  color: $nav-color;
}
/* 编译后 */
nav {
  width: 100px;
  color: #F90;
}
```

### 变量引用

```css
$highlight-color: #F90;
$highlight-border: 1px solid $highlight-color;
.selected {
  border: $highlight-border;
}
/* 编译后 */
.selected {
  border: 1px solid #F90;
}
```

### 中划线和下划线

```css
$link-color: blue;
a {
  color: $link_color;
}
/* 编译后 */
a {
  color: blue;
}
```

`$link-color`和`link_color`指向的是同一个变量，`sass`的大多数地方，中划线命名的内容和下划线命名的内容互通。

## 嵌套CSS规则

```css
#content {
  article {
    h1 { color: #333 }
    p { margin-bottom: 1.4em }
  }
  aside { background-color: #EEE }
}
/* 编译后 */
#content article h1 { color: #333 }
#content article p { margin-bottom: 1.4em }
#content aside { background-color: #EEE }
```

同时为容器元素及其子元素编写样式

```css
#content {
  background-color: #f5f5f5;
  aside { background-color: #eee }
}
/* 编译后 */
#content { background-color: #f5f5f5 }
#content aside { background-color: #eee }
```

### 父选择器的标识符&;

`sass`在解开一个嵌套规则时会把父选择器(`#content`)通过一个空格链接到子选择起的前面(`article`和`aside`)形成(`#content article`和`#content aside`)。即一个空格的后代选择器。

当为链接写`:hover`这种伪类时
```css
article a {
  color: blue;
  :hover { color: red }
}
```

会被编译成`article a :hover`

可以使用父选择器`&`
```css
article a {
  color: blue;
  &:hover { color: red }
}
/* 编译后 */
article a { color: blue }
article a:hover { color: red }
```

### 群组选择器的嵌套

```css
.container {
  h1, h2, h3 {margin-bottom: .8em}
}
/* 编译后 */
.container h1, .container h2, .container h3 { margin-bottom: .8em }
```

```css
nav, aside {
  a {color: blue}
}
/* 编译后 */
nav a, aside a {color: blue}
```

### 子组合选择器和同层组合选择器：>、+和~

```css
article {
  ~ article { border-top: 1px dashed #ccc }
  > section { background: #eee }
  dl > {
    dt { color: #333 }
    dd { color: #555 }
  }
  nav + & { margin-top: 0 }
}
/* 编译后 */
article ~ article { border-top: 1px dashed #ccc }
article > footer { background: #eee }
article dl > dt { color: #333 }
article dl > dd { color: #555 }
nav + article { margin-top: 0 }
```

### 嵌套属性

比如`border-*`等属性

```css
nav {
  border: {
  style: solid;
  width: 1px;
  color: #ccc;
  }
}
/* 编译后 */
nav {
  border-style: solid;
  border-width: 1px;
  border-color: #ccc;
}
```
对于属性的缩写形式，可以像下边来嵌套

```css
nav {
  border: 1px solid #ccc {
  left: 0px;
  right: 0px;
  }
}
/* 编译后 */
nav {
  border: 1px solid #ccc;
  border-left: 0px;
  border-right: 0px;
}
```

## 导入SASS文件

`CSS`的`@import`规则允许在`CSS`文件中导入其他`CSS`文件。只有执行到`@import`时，浏览器才会去下载其他`CSS`文件，这导致页面加载起来特别慢。

`SASS`的`@import`规则在生成`CSS`文件时就把相关文件导入进来。

`SASS`的`@import`规则并不需要指明被导入文件的全名，可以省略.sass或者.scss后缀。

### 使用SASS部分文件

`sass`局部文件文件名以下划线开头，这样`SASS`就不会在编译时单独编译这个文件输出`CSS`，只把这个文件做导入。

想导入`themes/_night-sky.scss`这个局部文件里的变量，只需要在样式表中写`@import "themes/night-sky";`。

### 默认变量值

```css
$fancybox-width: 200px;
$fancybox-width: 400px !default;
.fancybox {
width: $fancybox-width;
}
/* 编译后 */
.fancybox { width: 200px; }
```

`SASS`的`!default`标签很像`!important`标签的对立面，如果这个变量被声明赋值了，那就用它声明的值，否则就用这个默认值。

### 嵌套导入

有一个名为`_blue-theme.scss`
```css
aside {
  background: blue;
  color: white;
}
```

导入到一个CSS中

```css
.blue-theme {@import "blue-theme"}

//生成的结果跟你直接在.blue-theme选择器内写_blue-theme.scss文件的内容完全一样。

.blue-theme {
  aside {
    background: blue;
    color: #fff;
  }
}
```

### 原生CSS导入

### 静默注释

`SASS`中注释为`//`

## 混合器

变量无法应付重用大段样式代码的情况，通过`SASS`的混合器实现大段样式的重用。

混合器使用`@mixin`标识符定义

```css
@mixin rounded-corners {
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}
```

```css
notice {
  background-color: green;
  border: 2px solid #00aa00;
  @include rounded-corners;
}

//sass最终生成：
.notice {
  background-color: green;
  border: 2px solid #00aa00;
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}
```

### 何时使用混合器

混合器的命名也要符合语义化

### 混合器中的CSS规则

```css
@mixin no-bullets {
  list-style: none;
  li {
    list-style-image: none;
    list-style-type: none;
    margin-left: 0px;
  }
}
```

```css
ul.plain {
  color: #444;
  @include no-bullets;
}
/* 编译后 */
ul.plain {
  color: #444;
  list-style: none;
}
ul.plain li {
  list-style-image: none;
  list-style-type: none;
  margin-left: 0px;
}
```

### 给混合器传参

`@include`混合器可以传参数

```css
@mixin link-colors($normal, $hover, $visited) {
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}
```

```css
a {
  @include link-colors(blue, red, green);
}

//Sass最终生成的是：

a { color: blue; }
a:hover { color: red; }
a:visited { color: green; }
```

### 默认参数值

> 为了在@include混合器时不必传入所有的参数，我们可以给参数指定一个默认值。参数默认值使用$name: default-value的声明形式，默认值可以是任何有效的css属性值，甚至是其他参数的引用

```css
@mixin link-colors(
    $normal,
    $hover: $normal,
    $visited: $normal
  )
{
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}
```

## 使用选择器继承来精简CSS

通过`@extend`语法实现继承
```css
//通过选择器继承继承样式
.error {
  border: 1px solid red;
  background-color: #fdd;
}
.seriousError {
  @extend .error;
  border-width: 3px;
}
/* 编译后 */
.error, .seriousError {
  border: 1px solid red;
  background-color: #fdd; 
}

.seriousError {
  border-width: 3px; 
}
```

任何跟`error`有关的选择器都会被`seriousError`继承

```css
.error a{  //应用到.seriousError a
  color: red;
  font-weight: 100;
}
h1.error { //应用到hl.seriousError
  font-size: 1.2rem;
}
/* 编译后 */
.error a, .seriousError a {
  color: red;
  font-weight: 100;
}

h1.error, h1.seriousError {
  font-size: 1.2rem;
}
```

### 何时使用继承

比如bootstrap中的按钮

+ btn
+ btn-primary
+ btn-tiny

### 继承的高级用法

最常用的用法是继承一个`html`元素的样式

```css
.disabled {
  color: gray;
  @extend a;
}
```