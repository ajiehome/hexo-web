---
title: Centos安装RabbitMQ
date: 2023-06-07 15:58:40
tags:
categories: 
- 操作系统
---
# Centos安装RabbitMQ

## 安装Erlang环境
```shell
# 1、安装依赖项
yum install -y epel-release

# 2、添加存储库条目
wget https://packages.erlang-solutions.com/erlang-solutions-1.0-1.noarch.rpm
rpm -Uvh erlang-solutions-1.0-1.noarch.rpm

# 3、安装
yum install -y erlang

# 4、验证是否安装成功
erl -version
```

## rabbitmq的命令

```shell
# 开启web管理界面
rabbitmq-plugins enable rabbitmq_management
# 添加用户
rabbitmqctl add_user admin admin
# 设置用户对 '/ 的权限
rabbitmqctl set_permissions -p "/" "admin" ".*" ".*" ".*"
# 设置用户角色
rabbitmqctl set_user_tags admin administrator
```