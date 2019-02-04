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
docker container stop <hash> // 停止容器
docker container kill <hash> // 强行kill容器
docker container rm <hash> // 删除容器
docker container rm $(docker container ls -a -q) // 删除所有容器
```
