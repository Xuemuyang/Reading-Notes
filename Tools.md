# 开发小工具

## curl

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