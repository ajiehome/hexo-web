---
title: Centos7安装Wordpress
date: 2023-06-07 15:58:40
tags:
categories: 
- 操作系统
---
# Centos7安装Wordpress以及环境

## 一、安装MySQL57版本

**1、从MySQL官网下载YUM源的RPM安装包**

```shell
wget http://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm
```

**2、从下载好的rpm包安装MYSQL**

```shell
yum localinstall mysql57-community-release-el7-8.noarch.rpm
```

**3、安装MySQL服务**

```shell
yum install mysql-community-server
```

**4、启动MySQL服务**

```shell
systemctl start mysqld.service
```

**5、设置开机自启并且刷新**

```shell
systemctl enable mysqld
systemctl daemon-reload
```

**6、修改默认初始化密码->由于密码策略所以只能设置较为复杂的密码**

```shell
#临时密码在/var/log/mysqld.log
alter user 'root'@'localhost' identified by 'ZhonHua@5000Nian';
```

**7、查看MySQL的密码安全策略**

```shell
show variables like 'validate_password%';
```

![img](./images/MYSQL密码安全策略.PNG)

**8、修改密码杂度为最低以及把密码长度改为自己想要的**

```shell
set global validate_password_policy = LOW;
set global validate_password_length = 6;
```

![img](./images/MySQL修改密码策略.PNG)

**9、修改为自己想要的密码**

```shell
alter user 'root'@'localhost' identified by '123456';
```

**10、创建wordpress数据库**

```shell
create database wordpress;
```



**11、允许远程登陆然后刷新**

```shell
grant all on *.* to root@'%' identified by 'password' with grant option;
flush privileges;
```

**12、重启MySQL服务**

```shell
systemctl restart mysqld.service
```

**13、密码忘记或者策略失败**

```shell
#关闭密码验证策略，url：vi /root/my.cnf
skip-grant-tables
#修改mysql.user表的密码
update mysql.user set authentication_string=password('新密码') where user='root' ;
#刷新
flush privileges ;
#然后删除skip-grant-tables之后重启MySQL服务
```

## 二、安装apache->httpd

**1、安装httpd**

```shell
yum install httpd
```

**2、启动httpd服务**

```shell
systemctl start httpd.service
```

**3、设置成开机自启并且刷新**

```shell
systemctl enable httpd.service
systemctl daemon-reload
```

## 三、安装php->及相关驱动

**1、安装php、php-fpm、php-mysql**

```shell
yum install php php-fpm php-mysql
```

**2、启动php-fpm服务**

```shell
systemctl start php-fpm.service
```

**3、设置成开机自启并且刷新**

```shell
systemctl enable php-fpm.service
systemctl daemon-reload
```

## 四、查看三个服务的运行状态

**httpd监听80端口，mysql监听3306端口，php-fpm监听9000端口**
```shell
netstat -tunlp
```

## 五、安装wordpress官网tar.gz安装包

**2、用wget获取wordpress安装包**

```shell
wget https://ww.wp.xz.cn/wordpress-4.5.20.tar.gz
```

**3、解压安装包到/var/www路径下**

```shell
tar -zxf wordpress-4.5.20.tar.gz -C /var/www
```

**4、修改httpd的配置文件把默认的映射路径改为/var/www/wordpress**  

```shell
vi  /etc/httpd/conf/httpd.conf
# DocumentRoot的值

**5、保存修改文件退出后重新启动httpd服务**

```shell
systemctl restart httpd.service
```

**6、把wordpress目录赋予apache所有权限**

```shell
chown -R apache:apache /var/www/wordpress
```
## 六、用浏览器启动绑定服务器的域名或者服务器的IP然后就可以开始配置相关的数据