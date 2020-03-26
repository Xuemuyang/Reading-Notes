# docker

## 常用命令

```sh
docker // 显示命令
docker container --help // 显示命令
docker --version // 显示版本
docker version // 显示版本
docker info // 显示信息
docker image ls // 显示镜像
docker container ls // 显示运行的容器
docker container ls --all // 所有的容器
docker container ls -aq // quiet模式的容器
docker build -t friendlyhello . // 使用目录中的Dockerfile来构建friendlyhello镜像
docker run hello-world // 跑hello-word镜像
docker run -p 4000:80 friendlyhello // 跑friendlyhello镜像，local 4000 -> docker 80
docker run -d -p 4000:80 friendlyhello // detach模式运行
docker run --name myredis -d -p6379:6379 redis // --name指定名称
docker container stop <hash> // 停止容器
docker container kill <hash> // 强行kill容器
docker container rm <hash> // 删除容器
docker container rm $(docker container ls -a -q) // 删除所有容器
```

## 疑问

- 简介
  - 可以用来干什么
- Docker 的核心概念
  - Docker 引擎
  - 镜像
  - Registry
  - 容器
- 原理
- 守护进程

## 第一本 Docker 书

> 开源不再是以往自由软件(Free Software)的精神，而是如今商业社会的重要组成部分。

> Docker 是革命性的，重新定义了软件开发、测试、交付和部署的流程。我们交付的东西不再是代码、配置文件、数据库定义等，而是整个应用程序运行环境。

Infrastructure as code

- Docker 容器有很高的性能
- 开发环境和生产环境一致性
- 缩短代码从开发、测试到部署上线的周期，使应用程序具备可移植性，易于构建和协作
- Docker 推荐单个容器只运行一个应用程序或进程，分布式

### 安装 Docker

先决条件：

- 64 位，不支持 32 位，目前只能是 x86_64 和 amd64
- 对 Linux 内核版本有要求

OSX 直接使用安装包进行安装

