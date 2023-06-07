---
title: Kafka安装
date: 2023-06-07 15:58:40
tags:
categories: 
- 中间件
---
首先下载Kafka的二进制包，以及Kafka-Manager Cmak工具

[Kafka](https://downloads.apache.org/kafka/3.3.1/kafka_2.13-3.3.1.tgz)(https://downloads.apache.org/kafka/3.3.1/kafka_2.13-3.3.1.tgz)

[Cmak](https://github.com/yahoo/CMAK/releases/download/3.0.0.6/cmak-3.0.0.6.zip)(https://github.com/yahoo/CMAK/releases/download/3.0.0.6/cmak-3.0.0.6.zip)

解压Kafka到`/opt`目录shell

```shell
tar -zxvf kafka_2.13-3.3.1.tgz -C /opt
```

解压Cmak到`/opt`目录

```shell
unzip cmak-3.0.0.6 -C /opt
```

修改cmak的application.properties`/opt/cmak-3.0.0.6/conf/application.properties`的如下信息

```shell
# The address the socket server listens on. If not configured, the host name will be equal to the value of
# java.net.InetAddress.getCanonicalHostName(), with PLAINTEXT listener name, and port 9092.
#   FORMAT:
#     listeners = listener_name://host_name:port
#   EXAMPLE:
#     listeners = PLAINTEXT://your.host.name:9092
#listeners=PLAINTEXT://:9092

listeners=PLAINTEXT://10.254.0.2:9092

# Listener name, hostname and port the broker will advertise to clients.
# If not set, it uses the value for "listeners".
#advertised.listeners=PLAINTEXT://your.host.name:9092
advertised.listeners=PLAINTEXT://10.254.0.2:9092

# Settings prefixed with 'kafka-manager.' will be deprecated, use 'cmak.' instead.
# https://github.com/yahoo/CMAK/issues/713
kafka-manager.zkhosts="kafka-manager-zookeeper:2181"
#kafka-manager.zkhosts=${?ZK_HOSTS}
kafka-manager.zkhosts="10.254.0.2:2181"
cmak.zkhosts="kafka-manager-zookeeper:2181"
#cmak.zkhosts=${?ZK_HOSTS}
cmak.zkhosts="10.254.0.2:2181"
```

构建如下脚本，一键启动Kafka所需要的Zookeeper服务、Kafka服务以及Cmak管理工具

```shell
#!/bin/bash

option=$1

echo "option $option"

case $option in 

  'start')

    # create kafka catalogue
    if [ -d '/root/kafka' ]; then
      echo 'kafka catalogue exists'
    else 
      mkdir /root/kafka
      echo 'create kafka catalogue success'
    fi
    # create kafka log catalogue
    if [ -d "/root/kafka/log" ]; then
      echo 'kafka log catalogue exists'
    else
      mkdir /root/kafka/log
      echo 'create kafka log catalogue success'
    fi
    #  create kafka pid catalogue
    if [ -d '/root/kafka/pid' ]; then
      echo 'kafka pid catalogue exists'
    else
      mkdir /root/kafka/pid
      echo 'create kafka pid catalogue success'
    fi
    # creare kafka zk log file 
    if [ -f '/root/kafka/log/zk.log' ]; then
      echo 'kafka zk log file already exists'
    else 
      touch /root/kafka/log/zk.log
      echo 'create kafka zk log file success'
    fi
    # create zk pid file 
    if [ -f '/root/kafka/pid/zk-pid' ]; then
      echo 'kafka zk pid file already exists'
    else
      touch /root/kafka/pid/zk-pid
      echo 'create kafka zk pid file success'
    fi

    nohup  /opt/kafka_2.13-3.3.1/bin/zookeeper-server-start.sh /opt/kafka_2.13-3.3.1/config/zookeeper.properties > /root/kafka/log/zk.log 2>&1 &
    echo $!
    echo $! > /root/kafka/pid/zk-pid
    echo 'loading 1 minute ......' 
    sleep 1m

    # create kafka server log file
    if [ -f '/root/kafka/log/kafka.log' ]; then
      echo 'kafka server log file exists'
    else 
      touch /root/kafka/log/kafka.log
      echo 'create kafka server log file success'
    fi
    # create kafka pid pid file
    if [ -f '/root/kafka/pid/kafka-pid' ]; then
      echo 'kafka server pid file exists'
    else
      touch /root/kafka/pid/kafka-pid
      echo 'create kafka server pid file success'
    fi

    export JMX_PORT=9999

    nohup /opt/kafka_2.13-3.3.1/bin/kafka-server-start.sh /opt/kafka_2.13-3.3.1/config/server.properties > /root/kafka/log/kafka.log 2>&1 &
    echo $!
    echo $! > /root/kafka/pid/kafka-pid


    # create kafka-manager cmak log file
      if [ -f '/root/kafka/log/cmak.log' ]; then
        echo 'kafka manager cmak log file exists'
      else
        touch /root/kafka/log/cmak.log
        echo 'create kafka manager cmak log file success'
      fi
    # create kafka-manager cmak pid file
     if [ -f '/root/kafka/pid/cmak-pid' ]; then
       echo 'kafka manager cmak pid file exists'
     else 
       touch /root/kafka/pid/cmak-pid
       echo 'create kafka manager cmak pid file success'
     fi

    nohup bash /opt/cmak-3.0.0.6/bin/cmak > /root/kafka/log/cmak.log 2>&1 &
    echo $!
    echo $! > /root/kafka/pid/cmak-pid

    echo 'loading 1 minute ......'
    sleep 1m
    echo 'all server start success'
  ;;

  'stop')
    zk_pid=$(cat /root/kafka/pid/zk-pid)
    kill -9 $zk_pid
    echo 'kill zookeeper server success'

    kafka_pid=$(cat /root/kafka/pid/kafka-pid)
    kill -9 $kafka_pid
    echo 'kill kafka server success'

    cmak_pid=$(cat /root/kafka/pid/cmak-pid)
    kill -9 $cmak_pid
    echo 'kill kafka manager cmak success'     
    rm -rf /opt/cmak-3.0.0.6/RUNNING_PID
  ;;

  *)
    echo 'option error'
  ;;
esac
```
