---
title: Windows安装MySQL
date: 2023-06-07 15:58:40
tags:
categories: 
- 数据库
---
# Windows下MySQL的安装
首先下载MySQL的的压缩包（MySQL社区版）

[MySQL5.7社区版](https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.37-winx64.zip)

## 安装
如果以前安装过其它版本的MySQL那么需要先卸载mysql的服务在进行安装
卸载方式：以管理员身份进入CMD控制台，然后输入一下命令进行删除服务
如果未安装则可以忽略这一步骤：mysql是服务的名称
```shell
sc delete mysql
```
## 1、MySQL的解压
将下载好的mysql的压缩包解压，并且把解压之后的mysql-8.0.15-winx64文件夹放到安装文件夹
我的是放在：D:\Software\MySQL\mysql-8.0.15-winx64
## 2、配置环境变量
右键我的计算机->属性->高级系统设置->环境变量
新建系统变量
```shell
变量名称：MYSQL_HOME
变量路径：D:\Software\MySQL\mysql-8.0.15-winx64\bin
```
修改path变量，加入
```shell
%MYSQL_HOME%\bin
```
**注意：**如果失败，可以创建一个指定初始配置文件(我现在这个版本是没有配置的)
初始化mysql前，可以通过ini文件来指定部分初始配置，比如`basedir`和`datadir`等，当然，也可以不指定利用默认的，参考文档[官方文档](https://dev.mysql.com/doc/refman/8.0/en/windows-create-option-file.html)
在mysql的根目录下，创建mysql.ini文件，加入下面内容
```yml
[mysqld]
# set basedir to your installation path
basedir=D:\Software\MySQL\mysql-8.0.15-winx64
# set datadir to the location of your data directory
datadir=D:\Software\MySQL\mysql-8.0.15-winx64\data
```
## 3、初始化
打开cmd控制台，输入初始化命令（由于配置了系统的环境变量，所以不需要进入到D:\Software\MySQL\mysql-8.0.15-winx64\bin下）
```shell
//生成临时密码
mysqlid --initialize --console
```
或者
```shell
//空密码
mysqld --initialize-insecure --console
```
如果选择临时密码，控制台会有以下输出，可以看到里面有临时密码（temporary password is generated for root@localhost:后面的值，注意去掉空格），请一定要记住，当然你如果不想记住也可以，只是需要再折腾一下去重置密码
```yml
2018-08-15T02:55:43.924361Z 0 [System] [MY-013169] [Server] C:\MyPrograms\mysql-8.0.12-winx64\bin\mysqld.exe (mysqld 8.0.12) initializing of server in progress as process 12040
2018-08-15T02:55:55.962035Z 5 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: XCeQtsgMO7-F
2018-08-15T02:56:03.261174Z 0 [System] [MY-013170] [Server] C:\MyPrograms\mysql-8.0.12-winx64\bin\mysqld.exe (mysqld 8.0.12) initializing of server has completed
```
## 4、安装windows服务
用管理员身份打开CMD，在里面输入如下命令
```shell
//serviceName为服务名，不输入默认为mysql
mysqld --install [serviceName]
```
如果看到下面内容，则证明服务安装成功
```
Service successfully installed.
```
## 5、启动服务
还是用管理员身份打开CMD，输入如下命令
```shell
net start mysql

//以下输出证明启动成功
MySQL 服务正在启动 ..
MySQL 服务已经启动成功。

//如果你要关闭的话
net stop mysql
```
## 6、更改密码
连接到mysql
```shell
//下面-p后面的内容就是临时密码
mysql -uroot -pXCeQtsgMO7-F
```
修改密码
```shell
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'newpassword';
```
## 总结
* 1、环境变量是为了在命令行CMD中更加方便使用mysql命令；
* 2、如果有之前有安装过其他版本mysql，记得先卸载并删除服务；
* 3、安装完记得登录并修改密码，不论是采用空密码还是临时密码；
* 4、当然，命令行终归是不方便的，现在有很多可视化界面，如：Navicat等。
