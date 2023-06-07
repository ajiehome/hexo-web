---
title: MySQL命令集
date: 2023-06-07 15:58:40
tags:
categories: 
- 数据库
---
# MySQL命令集

**MySQL连接**

```shell
#本地连接
#password:可直接填写密码，或者直接回车，然后再保密输入密码
mysql -u root -p "password";
#远程连接
mysql -h "ip" -u root -p "password";
```

**修改密码**

进入mysql的bin目录下运行cmd

```shell
#没有密码时可以省略 -p 参数
mysqladmin  -uroot -p "old password" password "new password";
```

**新建用户**

```shell
grant [select,insert,update,delete] on "database".* to "user name"@"login host" identified by "password";
```

例如：新建一个number1用户，密码为123456，该用户只有对db1数据库所有的表有读取权限，且只能在MySQL所在的主机上登录，取消密码则重复这行命令，只是把密码置空

```shell
grant select on db1.* to number1@localhost identified by "123456";
```



**查询连接**

```shell
# 查询连接超时时间
show variables like '%timeout%';
# 查询连接数
show status like  'Threads%';
```

Threads_connected ：这个数值指的是打开的连接数.

Threads_running ：这个数值指的是激活的连接数，这个数值一般远低于connected数值.

Threads_connected 跟show processlist结果相同，表示当前连接数。准确的来说，Threads_running是代表当前并发数

```shell
#查询最大连接数
show variables like 'max_connections';
#修改最大连接数
#方法一
set global max_connections = 1000;
#方法二 修改my.conf配置文件：加上或者修改max_connections
max_connections = 1000

```

