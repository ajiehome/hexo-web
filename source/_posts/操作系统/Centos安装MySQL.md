---
title: Centos安装MySQL
date: 2023-06-07 15:58:40
tags:
categories: 
- 操作系统
---
# Centos安装MySQL

#### 1、首先更新yum命令

```shell
yum update -y
```

#### 3、下载MySQL的npm源

```shell
wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
```


#### 4、添加MySQL的npm源

```shell
yum localinstall mysql80-community-release-el7-3.noarch.rpm
```

#### 5、查看当前的MySQL版本

```shell
yum repolist enabled | grep "mysql.*-community.*"
```


#### 6、查看所有MySQL可用版本

```shell
yum repolist all | grep mysql
```

#### 7、关闭/禁用8.0的MySQL版本并且启用5.7的版本

```shell
yum-config-manager --disable mysql80-community

yum-config-manager --enable mysql57-community
```


#### 8、再次查看当前版本（正则匹配）

```shell
yum repolist enabled | grep mysql
```

#### 9、安装MySQL服务

```shell
yum install -y mysql-community-server
```

#### 10、启用MySQL服务

```shell
systemctl start mysqld
```

#### 11、查看临时默认密码，且修改密码

```shell
cat /var/log/mysqld.log
# 由于密码策略所以只能设置较为复杂的密码
alter user 'root'@'localhost' identified by 'ZhonHua@5000Nian';
```

#### 12、查看MySQL的密码安全策略

```shell
show variables like 'validate_password%';
```

#### 13、修改密码杂度为最低以及把密码长度改为自己想要的

```shell
set global validate_password_policy = LOW;
set global validate_password_length = 6;
```

#### 14、修改密码为自己想要的

```shell
alter user 'root'@'localhost' identified by '123456';
```

#### 15、允许远程登陆然后刷新

```shell
# 允许登录
grant all on *.* to root@'%' identified by 'you new password' with grant option;
# 刷新
flush privileges;
```
you new password：设置的新密码

#### 16、重启MySQL服务

```shell
systemctl restart mysqld.service
```

---

[详情参照官方文档](https://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/)
