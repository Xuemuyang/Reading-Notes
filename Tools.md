# 开发小工具

## curl

参考 [curl 的用法指南](https://www.ruanyifeng.com/blog/2019/09/curl-reference.html)

1.查看网页源码

```sh
$curl www.baidu.com
```

将网页保存下来

```sh
$curl -o [文件名] www.baidu.com
```

2.显示头信息

```sh
$curl -i www.baidu.com
```

可以显示http response的头信息，连同网页代码一起

3.发送请求

curl默认的HTTP动词是GET

```sh
$curl example.com/form.cgi?data=xxx
```

使用-X参数支持其他动词

```sh
$curl -X POST --data "data=xxx" example.com/form.cgi
```

## aria2c

```sh
brew install aria2
ps -ef|grep aira2c //查看aria2c进程
aria2c --conf-path="/Users/mac/.aria2/aria2.conf" -D
```

## npx

参考 [npx 使用教程-阮一峰](http://www.ruanyifeng.com/blog/2019/02/npx.html)

### 调用项目安装的模块

一般来说需要在 package.json 的 `scripts` 字段里面通过 `npm run scripts` 调用项目依赖。

```sh
npx mocha --version
```

### 避免全局安装模块

npx 避免全局安装的模块，比如 `create-react-app`，使用 npx 运行它且不进行全局安装。

可以使用 `@` 指定模块版本

```sh
npx create-react-app@x.x.x my-react-app
```

### 使用不同版本的node

```sh
npx node@0.12.8 -v
```

## Chrome 控制台

