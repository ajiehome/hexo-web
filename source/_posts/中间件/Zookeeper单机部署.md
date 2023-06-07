---
title: Zookeeper单机部署
date: 2023-06-07 15:58:40
tags:
categories: 
- 中间件
---
---
title: Zookeeper单机部署
date: 2022-10-10 13:59:49.654
updated: 2022-10-13 14:44:16.087
url: /archives/zookeeper-dan-ji-bu-shu
categories: 
- Zookeeper
tags: 
- Zookeeper
---

## 环境
> CentOS7、openJdk11

## 步骤
1. 首先使用`wget`下载zk的二进制包
	```shell 
    wget https://dlcdn.apache.org/zookeeper/zookeeper-3.8.0/apache-zookeeper-3.8.0-bin.tar.gz
    ````
2. 解压出到opt目录下
   ```shell
   tar -zxvf apache-zookeeper-3.8.0-bin.tar.gz -O /opt
   ```
3. 配置systemd服务管理
	在/etc/systemd/system目录下创建zkServer.service文件
   ```shell
   [Unit]
   Description=zkServer

   [Service]
   Type=forking
   #PIDFile=/var/run/zkServer_2181.pid
   ExecStart=/opt/apache-zookeeper-3.8.0-bin/bin/zkServer.sh start
   ExecStartPost=/bin/sleep 0.1
   ExecReload=/bin/kill -s HUP $MAINPID
   ExecStop=/opt/apache-zookeeper-3.8.0-bin/bin/zkServer.sh stop
   PrivateTmp=true

   [Install]
   WantedBy=multi-user.target
   ```
   [含义参考](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)
4. 使用systemd启用服务即可
	```shell
    # 启用
    systemctl start zkServer
    # 停止
    systemctl stop zkServer
    # 重启
    systemctl restart zkServer
    ```