# Angular

## 菜鸟教程

### AngularJS 简介

Angular通过`ng-directives`扩展HTML

### AngularJS 表达式

+ {{}}双括号语法
+ HTML属性中使用ng-bind

### AngularJS 指令

+ `ng-app` 初始化一个AngularJS应用程序
+ `ng-model` 把元素值绑定到应用程序

`ng-repeat`会重复一个HTML元素

```html
<div ng-app="" ng-init="names=['Jani','Hege','Kai']">
  <p>使用 ng-repeat 来循环数组</p>
  <ul>
    <li ng-repeat="x in names">
      {{ x }}
    </li>
  </ul>
</div>
```

### AngularJS `ng-model`指令

`ng-model`指令可以将输入框的值与AngularJS创建的变量绑定

### AngularJS Scope(作用域)

Scope(作用域)是应用在HTML(视图)和JavaScript(控制器)之间的纽带。

Scope是一个对象，有可用的方法和属性。

Scope可应用在视图和控制器上。

```html
<div ng-app="myApp" ng-controller="myCtrl">
    <input ng-model="name">
    <h1>{{greeting}}</h1>
    <button ng-click='sayHello()'>点我</button>
</div>

<script>
var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope) {
    $scope.name = "Runoob";
    $scope.sayHello = function() {
        $scope.greeting = 'Hello ' + $scope.name + '!';
    };
});
</script>
```

#### 根作用域

所有的应用都有一个`$rootScope`，可以作用在`ng-app`指令包含的所有HTML

### AngularJS 控制器

一个`ng-app`中可以有多个`ng-controller`

### AngularJS 过滤器

有内置过滤器，也有自定义过滤器

### AngularJS 服务(service)

所谓服务就是内部封装好的工具函数，比原生的nb

### AngularJS HTTP

```html
<div ng-app="myApp" ng-controller="siteCtrl">

<ul>
  <li ng-repeat="x in names">
    {{ x.Name + ', ' + x.Country }}
  </li>
</ul>

</div>

<script>
var app = angular.module('myApp', []);
app.controller('siteCtrl', function($scope, $http) {
  $http.get("http://www.runoob.com/try/angularjs/data/sites.php")
  .success(function (response) {$scope.names = response.sites;});
});
</script>
```

### AngularJS Select

使用`ng-options`指令来创建一个下拉列表

```html
<div ng-app="myApp" ng-controller="myCtrl">

<select ng-init="selectedName = names[0]" ng-model="selectedName" ng-options="x for x in names">
</select>

</div>

<script>
var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope) {
    $scope.names = ["Google", "Runoob", "Taobao"];
});
</script>
```

### AngularJS 表格

使用`$index`显示序号

```html
<table>
  <tr ng-repeat="x in names">
    <td>{{ $index + 1 }}</td>
    <td>{{ x.Name }}</td>
    <td>{{ x.Country }}</td>
  </tr>
</table>
```

使用`$even`和`$odd`

```html
<table>
<tr ng-repeat="x in names">
<td ng-if="$odd" style="background-color:#f1f1f1">{{ x.Name }}</td>
<td ng-if="$even">{{ x.Name }}</td>
<td ng-if="$odd" style="background-color:#f1f1f1">{{ x.Country }}</td>
<td ng-if="$even">{{ x.Country }}</td>
</tr>
</table>
```

### AngularJS DOM

DOM元素提供了属性绑定的指令

#### ng-disabled

```html
<div ng-app="" ng-init="mySwitch=true">
<p>
<button ng-disabled="mySwitch">点我!</button>
</p>
<p>
<input type="checkbox" ng-model="mySwitch">按钮
</p>
<p>
{{ mySwitch }}
</p>
</div>
```

#### ng-show

```html
<div ng-app="" ng-init="hour=13">
<p ng-show="hour > 12">我是可见的。</p>
</div>
```

#### ng-hide

```html
<div ng-app="">
<p ng-hide="true">我是不可见的。</p>
<p ng-hide="false">我是可见的。</p>
</div>
```

### AngularJS 事件

`ng-click`指令

```html
<div ng-app="myApp" ng-controller="personCtrl">
<button ng-click="toggle()">隐藏/显示</button>
<p ng-hide="myVar">
名: <input type="text" ng-model="firstName"><br>
姓名: <input type="text" ng-model="lastName"><br>
<br>
Full Name: {{firstName + " " + lastName}}
</p>
</div>

<script>
var app = angular.module('myApp', []);
app.controller('personCtrl', function($scope) {
    $scope.firstName = "John",
    $scope.lastName = "Doe"
    $scope.myVar = false;
    $scope.toggle = function() {
        $scope.myVar = !$scope.myVar;
    };
});
</script>
```