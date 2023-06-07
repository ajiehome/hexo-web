---
title: RocketMQ与Systemd
date: 2023-06-07 15:58:40
tags:
categories: 
- 中间件
---
# RocketMQ与Systemd

RocketMQ的安装比较简单，只需要提供了JAVA_HOME环境，之后运行对于的脚本即可，所以这里主要介绍的RocketMQ与Systemd的配置，结合Shell脚本，一次性的启用namsesrv和borker以及web管理工具`rocketmq-dashboard`

## 版本

Java: openjdk-11

RocketMQ: `rocketmq-all-5.0.0-bin-release.zip`[下载](https://www.apache.org/dyn/closer.cgi?path=rocketmq/5.0.0/rocketmq-all-5.0.0-bin-release.zip)

RocketMQ Dashboard: `rocketmq-dashboard-rocketmq-dashboard-1.0.0.tar.gz` [源码地址,该项目需要自己编译成jar包](https://github.com/apache/rocketmq-dashboard)



在下载了RocketMQ的二进制包之后，解压到指定位置，而后配置对于的`namesrv`和`borker`服务，systemd的服务配置在`/usr/lib/systemd/system/`或者`/etc/systemd/system/`目录下

rmq_namesrv.service

```shell
[Unit]
Description=rocketmq namesrv
After=network.target

[Service]
Type=simple
ExecStart=/opt/rocketmq-all-5.0.0-bin-release/bin/mqnamesrv
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=//opt/rocketmq-all-5.0.0-bin-release/bin/mqshutdown namesrv

[Install]
WantedBy=mulitiple-rmq-namesrv.target
```

rmq_borker.service

```shell
[Unit]
Description=rocketmq broker
After=network.target

[Service]
Type=simple
ExecStart=/opt/rocketmq-all-5.0.0-bin-release/bin/mqbroker -n localhost:9876 --enable-proxy
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/opt/rocketmq-all-5.0.0-bin-release/bin/mqshutdown broker

[Install]
WantedBy=multiple-rmq-broker.target
```

在配置了这两个服务之后，即可通过systemctl start/stop/restart来执行服务了，由于每次都需要启动`namesrv`和`borker`服务还有web管理工具过于麻烦，所以引入`shell`脚本进行处理

rocketmq.shell

```shell
#!/bin/bash

instruct=$1

echo $instruct

case $instruct in
 'start')
   systemctl start rmq_namesrv
   systemctl start rmq_borker
   export NAMSESRV_ADDR=localhost:9876
   nohup java -jar /opt/rocketmq-dashboard-rocketmq-dashboard-1.0.0/target/rocketmq-dashboard-1.0.0.jar > /root/rocketmq-dashboard.log &
   echo $! > /root/rmq-d-pid
   echo "RocketMQ Start SUCCESS PID: " + $!
 ;;

 'stop')
   systemctl stop rmq_namesrv
   systemctl stop rmq_borker
   PID=$(cat /root/rmq-d-pid)
   kill -9 ${PID}
   echo "RocketMQ Stop SUCCESS"
 ;;

 *)
   echo "Input instruct error"
 ;;
esac
```

可以通过执行该shell脚本对RocketMQ进行启动和关闭
