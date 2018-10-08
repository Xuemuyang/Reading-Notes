# 微信小程序相关

## 坑

1. .vscode目录下的settings.json必须是标准JSON，否则会导致小程序反复重启
1. 测试域名会不让发送请求，在详情中开启不校验合法域名

## 官方文档

### 代码构成

#### JSON配置

+ `app.json`是当前小程序的全局配置，包括了小程序的所有页面路径、界面表现、网络超时时间、底部tab等。
+ `project.config.json`是工具配置
+ `page.json`是页面配置，可以覆盖一些全局配置

## 框架

### 目录结构

小程序的根目录

文件|必须|作用
---|---|---
app.js|是|小程序逻辑
app.json|是|小程序公共配置
app.wxss|否|小程序公共样式表

小程序的页面

文件|必须|作用
---|---|---
js|是|页面逻辑
wxml|是|页面结构
json|否|页面配置
wxss|否|页面样式表

### 视图层

#### WXML

##### 列表渲染

默认数组的当前项的下标变量名默认为`index`，数组当前项的变量名默认为`item`，不需要特别指定。

## 组件

### 视图容器

#### view

`view`是视图容器，`block`仅仅是一个包装元素，只接受控制属性，不在页面做任何渲染。

### 表单组件

#### form

`bindsubmit`属性用于指定事件处理函数，会携带form中的数据

#### button

`form-type`属性用于触发form组件的submit/reset事件

### 配置

## 概述

本质上是一套Hybrid解决方案

## 开发基础知识

+ 在IOS上，小程序的JavaScript运行在JavaScriptCore中
+ 在Android上，小程序的JavaScript代码通过X5内核来解析
+ 在开发工具上，小程序的JavaScript代码运行在NW.js(Chromium内核)中

### 项目配置

+ `project.config.json`:这个是配置项目工具相关的，开发者工具的编译设置等
+ `app.json`:小程序的全局配置，包括所有页面路径、界面表现、网络超时时间、底部tab、插件等。常用配置是`window`和`pages`。
+ `page.json`:是相对于`app.json`更细粒度的单页面配置。

### 组件和插件

根据实现的不同，分为Web组件和Native组件。

插件可以理解为npm包，供大家使用。

### 开发基础

与react相似，小程序内对页面的数据修改只能通过`setData`方法，不能使用直接赋值的方式。

如果wxml中没有用到的属性，直接通过this.data.xxx来赋值没有问题。

> 修改页面的数据，只能使用`this.setData`修改！

#### 事件绑定

相对于HTML5中的触摸事件外，新增`tap`类的事件

事件名称|说明
---|---
tap|手指触摸马上离开
longpress|手指触摸后，超过350ms再离开，如果指定了callback并触发了事件，tap不再触发
longtap|手指触摸后，超过350ms再离开(推荐使用longpress事件代替)

#### 事件捕获

小程序内，触摸类事件支持捕获阶段，捕获先于冒泡触发，绑定捕获事件，可以使用`capture-bind`、`capture-catch`,后者将中断捕获阶段和取消冒泡阶段。

`bind`事件绑定不会阻止冒泡事件向上冒泡，`catch`事件绑定可以阻止冒泡事件向上冒泡。

如在下边这个例子中，点击 inner view 会先后调用handleTap3和handleTap2(因为tap事件会冒泡到 middle view，而 middle view 阻止了 tap 事件冒泡，不再向父节点传递)，点击 middle view 会触发handleTap2，点击 outer view 会触发handleTap1。

```js
<view id="outer" bindtap="handleTap1">
  outer view
  <view id="middle" catchtap="handleTap2">
    middle view
    <view id="inner" bindtap="handleTap3">
      inner view
    </view>
  </view>
</view>
```

## 小程序特点

小程序没有`window`、`document`，更像是一个Node.js宿主环境，小程序内不能使用`document.querySeletor`这类DOM选择器，也不支持`XMLHttpRequest`、`location`、`localStorage`等浏览器提供的API。

小程序并非通过URL访问，而是通过信道服务进行通信和会话管理，不支持Cookie存储，同时访问资源使用`wx.request`，不存在跨域问题。

微信小程序由逻辑层和视图层两个不同的线程进行交互而形成，而视图层是通过将`WXML`转换成JS，最终由JS 生成HTML片段放在WebView中显示的。