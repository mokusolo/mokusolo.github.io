---
title: 运行Node-RED官方docker后安装serial节点
date: 2018-10-12 20:11:56
tags: [Node-RED, docker, serial]
---
### 在ubuntu上安装docker
[Ubuntu 安装 Docker CE](https://yeasy.gitbooks.io/docker_practice/install/ubuntu.html)

### 在docker下运行Node-RED
[Running under Docker](https://nodered.org/docs/platforms/docker)

```
docker run -it -p 1880:1880 --name mynodered nodered/node-red-docker
```

[参数解释](https://hub.docker.com/r/nodered/node-red-docker/)

查看镜像
```
docker image ls
```

查看所有容器
```
docker container ls -a
```

访问127.0.0.1:1880后，发现没有所需的serial节点，要在容器中自行安装。

### 在容器中安装serial节点
[利用 commit 理解镜像构成](https://yeasy.gitbooks.io/docker_practice/image/commit.html)

进入该名为mynodered的容器
```
docker exec -it mynodered bash
```
终端就会从user@ubuntu之类的变成node-red@container_id这种:
[安装serial节点](https://flows.nodered.org/node/node-red-node-serialport)
```
npm i node-red-node-serialport
# ... 等待安装完成
exit
```

查看具体改动:
```
docker diff mynodered
```

### 提交为镜像
暂略