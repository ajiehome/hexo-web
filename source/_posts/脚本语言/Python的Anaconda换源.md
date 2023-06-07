---
title: Python的Anaconda换源
date: 2023-06-07 15:58:40
tags:
categories: 
- 脚本语言
---
# Anaconda 切换镜像源
清华镜像源，由于https不通，只能将所有的链接全部换成http
```yaml
channels:
  - defaults
show_channel_urls: true
default_channels:
  - http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```

基础命令
```shell
# 创建虚拟环境
conda crate -n py37 python=3.7
# 激活环境，windows不用conda
conda activate py37
# 删除虚拟环境
conda remove -n py37 --all
```
