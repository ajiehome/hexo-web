---
title: Docker的常用命令
date: 2023-06-07 15:58:40
tags:
categories: 
- 虚拟容器
---
# docker的常用命令

容器的创建

```shell
docker run -i ubuntu:15.10 
```

创建后台运行容器

```shell
docker run -itd [--name specified_name] ubuntu /bin/bash
```

- `docker`Docker的二进制执行文件
- `run `与docker组合来运行一个容器
- `/bin/bash`放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 /bin/bash
- `-t:`终端
- `-i:`交互式操作
- `-d:` 参数默认不会进入容器，想要进入容器需要使用指令 **docker exec**
- `ubuntu:15.10`指定需要运行的镜像，docker首先在本地查找是否有该镜像，如果没有的话就将从镜像库中获取对应的镜像

查看所有容器

```shell
docker ps -a
```

启动容器

```shell
docker start [mirror_name or mirror_id]
```

停止容器

```shell
docker stop [docker_name or docker_id]
```

删除容器

```shell
docker rm [specifind_name or specifind_id]
```

---

镜像获取

```shell
docker pull [mirror_name]
```

进入容器

```shell
docker exec -it [specifind_name or specifind_id] /bin/bash
docker attach [specifind_name or specifind_id]
```

- `exec:`此退出容器终端，不会导致容器的停止，推荐使用

导出容器

```shell
docker export [specifind_name or specifind_id] > ubuntu.tar
```

导入容器快照

```shell
cat ubuntu.tar | docker import - test/ubuntu:v1
```

通过URL导入快照

```shell
 docker import http://example.com/exampleimage.tgz example/imagerepo
```

![](http://www.ajiehome.cn/wp-content/uploads/2021/03/docker的所有镜像.png)

载入镜像

```shell
docker pull training/webapp
```

运行镜像程序

```shell
docker run -d -P training/webapp python app.py
```

- `-d:`让容器在后台运行。
- `-P:`将容器内部使用的网络端口随机映射到我们使用的主机上

![](http://www.ajiehome.cn/wp-content/uploads/2021/03/webapp运行图.png)

**PORTS**

- `0.0.0.0:49153->5000:`表明docker开放了5000端口，映射到了本地的49153端口