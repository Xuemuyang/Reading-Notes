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
