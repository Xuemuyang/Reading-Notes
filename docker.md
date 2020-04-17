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

- 镜像和容器的区别
- 如何从找到所有正在运行的镜像

## cheat sheet

镜像

```sh
docker images // 列出所有的镜像
docker pull ubuntu:12.04 // 拉取镜像，冒号后面的 12.04 称作标签，指定该仓库中的某一镜像
```

## 概念

- [COW (Copy-on-write)](https://zh.wikipedia.org/wiki/%E5%AF%AB%E5%85%A5%E6%99%82%E8%A4%87%E8%A3%BD)

## 第一本 Docker 书

> 云计算 “实例水平扩展，资源动态调整”

> 开源不再是以往自由软件(Free Software)的精神，而是如今商业社会的重要组成部分。

> Docker 是革命性的，重新定义了软件开发、测试、交付和部署的流程。我们交付的东西不再是代码、配置文件、数据库定义等，而是整个应用程序运行环境。

Infrastructure as code

- Docker 容器有很高的性能
- 开发环境和生产环境一致性
- 缩短代码从开发、测试到部署上线的周期，使应用程序具备可移植性，易于构建和协作
- Docker 推荐单个容器只运行一个应用程序或进程，进行分布式部署

### 安装 Docker

先决条件：

- 64 位，不支持 32 位，目前只能是 x86_64 和 amd64
- 对 Linux 内核版本有要求，3.8 或更高版本
- 内核必须支持适合的存储驱动(storage drive)
- 内核必须支持并开启 cgroup 和 命名空间(namespace) 功能

OSX 直接使用安装包进行安装

使用 `uname` 命令查看内核版本信息

有一个 Docker 安装脚本，`get.docker.com`

```sh
curl https://get.docker.com/ | sudo sh
```

### 镜像管理

可以部署本地仓库，也可以使用 Docker Hub，类似 npm

### Dockerfile

使用 Dockerfile 来构建镜像，使用 DSL(Domain Specific Language) 来构建一个 Docker 镜像

Dockerfile 由一系列指令和参数组成，每条指令，如 `FROM`，必须为大写字母，且后面需要有参数。Dockerfile 中的指令会按照顺序从上到下执行。

Dockerfile 中 `#` 开头的行是注释。

每个 Dockerfile 的第一条指令必须是 `FROM`。`FROM` 指令指定一个已经存在的镜像，后面的指令将基于该镜像进行，这个镜像被称为基础镜像(basic image)。

Dockerfile 有构建缓存
