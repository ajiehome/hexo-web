---
title: Docker初步使用
date: 2023-06-07 15:58:40
tags:
categories: 
- 虚拟容器
---
# docker初步使用

命令解析

```shell
docker run ubuntu:15.10 /bin/echo "Hello Word"
```

- `docker`Docker的二进制执行文件
- `run `与docker组合来运行一个容器
- `ubuntu:15.10`指定需要运行的镜像，docker首先在本地查找是否有该镜像，如果没有的话就将从镜像库中获取对应的镜像
- `/bin/echo "Hellow Word"`在启动的容器中执行的命令

交互式容器的运行

```shell
docker run -it ubuntu:15.10 /bin/bash
```

- `-i`运行对容器内的标准输入（STDIN）进行交互
- `-t`在新的容器内指定一个伪终端或者终端