# Restful

网站即软件，而且是一种新型的软件。

> 这种"互联网软件"采用客户端/服务器模式，建立在分布式体系上，通过互联网通信，具有高延时（high latency）、高并发等特点。

传统上，软件和网络是两个不同的领域。互联网的兴起使得两个领域开始融合。

## 起源

`REST`这个词，是`Roy Thomas Fielding`2000年在其博士论文中提出。

## 名称

`Representational State Transfer`表现层状态转化。

如果一个架构符合`REST`原则，称其为`RESTful`架构。

## 资源(Resources)

"表现层"实际上指的是"资源"(Resources)的"表现层"。

所谓"资源"(Resources)，就是网络上的一个实体，可以时一段文字、一张图片、一首歌曲、一种服务。可以用一个URI指向它。

获取这个资源，访问它的URI即可，URI是每个资源的独一无二的标识符。

所谓"上网"，就是与互联网上一系列的"资源"互动，调用它的URI。

URI/URL的区别

+ URI(Uniform Resource Identifier)
+ URL(Uniform Resource Locator)

一句话解释：URI和URL都定义了what the resource is。URL还定义了where the resource is。

## 表现层(Representation)

把"资源"具体呈现出来的形式，叫做"表现层"。

文本可以用txt格式展现，也可以HTML,JSON甚至可以采用二进制格式；图片可以是JPG也可以是PNG。

URI只代表资源的实体，不代表它的形式。

## 状态转化(State Transfer)

HTTP协议是一个无状态协议，每一次的请求是独立不相干的，网站需要cookie来作为session实现的基础。

客户端想要操作服务器，必须通过某种手段，让服务器端发生"状态转化"(State Transfer)。这种转化是建立在表现层之上的，所以就是"表现层状态转化"。

客户端用到的手段，只能是HTTP协议，四个表示操作方式的动词`GET`、`POST`、`PUT`、`DELETE`。分别对应四种基本操作:GET用来获取资源，POST用来新建资源（也可以用于更新资源），PUT用来更新资源，DELETE用来删除资源。

##综述

总结一下什么是`RESTful`架构

1. 每一个URI代表一种资源；
2. 客户端与服务器之间，传递着这种资源的某种表现层；
3. 客户端通过四个HTTP动词，对服务器端资源进行操作，实现“表现层状态转化”。

## 误区

有一些典型的设计误区

URI包含动词，“资源”表示一种实体，应该是名词。

URI中不应该加入版本号

# RESTful API 设计指南

探讨如何设计一套合理、好用的API。




