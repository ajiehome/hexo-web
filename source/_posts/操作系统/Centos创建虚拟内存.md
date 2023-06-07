---
title: Centos创建虚拟内存
date: 2023-06-07 15:58:40
tags:
categories: 
- 操作系统
---
# Linux创建虚拟内存

1. 创建swap文件夹，一般在根目录就好，swap通常代表虚拟内存
2. 创建swapfile文件，swapfile为伪装成内存的文件
   ```shell
    dd if=/dev/zero of=/swap/swapfile bs=1G count=4096
   ```
3. 将swapfile文件设置为swap分区文件
   ```shell
    mkswap /swap/swapfile
   ```
4. 激活swap区，并立即启用交换区文件
   ```shell
    swapon /swap/swapfile
   ```
5. 设置开机自启需要在`/etc/fstab`文件的末尾添加一行命令
   ```shell
   /swap/swapfile swap swap defaults 0 0
   ```