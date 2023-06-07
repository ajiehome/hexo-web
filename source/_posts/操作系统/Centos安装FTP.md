---
title: Centos安装FTP
date: 2023-06-07 15:58:40
tags:
categories: 
- 操作系统
---
# Centos安装FTP->简单安装

**1、查看是否安装了FTP**

```shell
rpm -qa | grep vsftpd
```

**2、没有安装->安装vsftp**

```shell
yum -y install vsftpd
```

**3、添加FTP用户并修改密码**

```shell
useradd ftpLogin -d /home/ftpLogin
passwd ftpLogin
```

**4、修改vsftpd.conf配置文件**

```shell
vi /etc/vsftpd/vsftpd.conf

#把允许匿名登陆改为不允许
anonymous_enable=YES ->  anonymous_enable=NO

#在最后一行加上默认的加载目录->也就是FTP目录
local_root=/home/ftpLogin/
```

**5、修改文件夹的用户权限**

```shell
chmod -R 777 /home/ftpLogin
```

**6、查看服务是否运行**

```shell
#active为绿色则是运行状态 如何非运行的话就重新启动一下服务
#重新启动
systemctl restart vsftpd
#启动服务
systemctl start vsftpd
#查看服务状态
systemctl status vsftpd
#设置自动启动
systemctl enable vsftpd

yum -y install ftp
```

**7、安装FTP测试**

```shell
#我的已经安装过了就不再测试
yum -y install ftp
#使用anonymous登陆 可以无密码登陆
ftp localhost
```

**8、关掉防火墙->放行20&21端口 让其他设备也可访问（也可以设置防火墙规则，放行端口组）**

```shell
#关掉防火墙
systemctl stop firewalld.service
#为了防止防火墙自启，设置的永久关闭
systemctl disable firewalld.service
```