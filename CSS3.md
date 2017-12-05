# CSS3

---
这里用来记录一些有趣的小例子

1. 边框可以透明
```css
border: 15px solid transparent;
```
2. 文字溢出部分显示省略号

```css
white-space: nowrap; //规定段落中文本不换行
text-overflow: ellipsis;
```

3. 字间距

```css
letter-spacing: 2px;
letter-spacing: -3px;
```

4. 伪元素实现平行四边形

```html
<a href="#yolo" class="button"><div>Click me</div></a>
<button class="button"><div>Click me</div></button>
```

```css
.button {
    position: relative;
    display: inline-block;
    padding: .5em 1em;
    border: 0; margin: .5em;
    background: transparent;
    color: #000;
    text-transform: uppercase;
    text-decoration: none;
    font: bold 200%/1 sans-serif;
}

.button::before {
    content: ''; 
    position: absolute;
    top: 0; right: 0; bottom: 0; left: 0;
    z-index: -1;
    background: #58a;
    transform: skew(45deg);
}
```

> 如果你给默认内联的元素应用这个效果，`display`属性需要设置为`inline-block`或`block`，否则应用的变换不会生效。内部元素也一样。

定位偏移量设置为0，自动继承父元素的宽高，需要`z-index`来到下面去，不然会覆盖内容

5. table-layout

```css
.calendar {
  table-layout: fixed;
}
```

默认为automatic，设置为`fixed`之后可以使表格每一列固定宽度，不受内容影响

6. pointer-events是元素不可被点击 

```css
.disabled {
   pointer-events: none;
}
```

7. 字体模糊效果
```css
.blur {
    color: transparent;
    text-shadow: 0 0 5px rgba(0,0,0,0.5);
}
```

## box-shadow

```css
box-shadow: inset 18px -19px 300px 71px rgba(0,0,0,0.75);
```
+ 默认为outline外部shadow,inset设置为内部shadow
+ 水平/竖直 方向偏移
+ blur 焦距
+ spread radius 扩散范围

## border-image
```css
border-image:url(/i/border.png) 30 30 stretch;
```

+ url
+ 水平/竖直 剪裁图片位置 可以用百分比
+ stretch或者round，重复方式

## background

+ background-origin 可以确定背景在content-box，或是在border-box

## text-shadow

为文本添加阴影

## @font-face

```css
<style> 
    @font-face {
        font-family: myFirstFont;
        src: url('Sansation_Light.ttf'),
            url('Sansation_Light.eot'); /* IE9+ */
    }

    div {
        font-family:myFirstFont;
    }
</style>
```

通过@font-face可以从服务器引入字体

## 2D转换

+ translate()
+ rotate()
+ scale()
+ skew()
+ matrix() 

## 3D转换

+ rotateX()

## 过渡

+ transition
