---
title: Centos7常用命令
date: 2023-06-07 15:58:40
tags:
categories: 
- 操作系统
---
# Centos7常用命令

**防火墙相关命令**

- 防火墙的开启、关闭、重启、与状态

   ```shell
    #开启
    systemctl start   firewalld.service
    #关闭
    systemctl stop    firewalld.service
    #重启
    systemctl restart firewalld.service
    #状态
    systemctl status  firewalld.service
    #设置开机自启
    systemctl enable  firewalld.service
    #关闭开机自启
    systemctl disable firewalld.service
    
    #指定IP放行指定端口
    firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.142.166" port protocol="tcp" port="5432" accept"
    ```

- 查看所有公开项目

   ```shell
    #查看所有公开项
    firewall-cmd --list-all
    #查看端口是否放行
    firewall-cmd --zone=public --query-port=80/tcp
    ```

- 防火墙放行端口或服务

   ```shell
    #添加放行端口
    firewall-cmd --zone=public --add-port=80/tcp --permanent
    #重新加载所有的设置项
    firewall-cmd --reload
    ```

  ```shell
    #添加放行服务
    firewall-cmd --zone=public --add-service=http --permanent
    firewall-cmd --reload
    ```

- 防火墙移除放行端口或服务

   ```shell
    #移除已经放行的端口
    firewall-cmd --zone=public --remove-port=80/tcp
    firewall-cmd --reload
    ```

  ```shell
    #移除已经放行服务
    firewall-cmd --zone=public --remove-service=http
    firewall-cmd --reload
    ```

**端口操作**

- 端口占用查询

   ```shell
    netstat -tunlp | grep 'port'
    ----------------------------------------------------------------------------------
    -t (tcp) 仅显示tcp相关选项
    -u (udp) 仅显示udp相关选项
    -n 拒绝显示别名，能显示数字的全部转化为数字
    -l 仅列出在Listen(监听)的服务状态
    -p 显示建立相关链接的程序名
    ```

   ```shell
    lsof -i:'port'
    ```

- 端口终结

   ```shell
    kill -9 'pid'
    ```

**服务操作**

- 服务状态查询

  ```shell
    systemctl status 'serverName'
    ```

- 服务开启

  ```shell
    sysyemctl start 'serverName'
    ```

- 服务关闭

   ```shell
    systemctl stop 'serverName'
    ```

- 服务自启

  ```shell
    systemctl enable 'serverName'
    ```

- 关闭服务自启

   ```shell
    systemctl disable 'serverName'
    ```

**用户和组**

- 用户和组

  ```shell
    #添加用户
    adduser 'userName'
  ```
  ```shell
  #新建工作组
  groupadd 'groupName'
  ```

  ```shell
  #添加用户，并且把把用户添加到指定名称的组
  useradd -g 'groupName' 'userName'
  ```

  ```shell
  #给已有用户添加到工作组
  usermod -G 'groupName' 'userName'
  gpasswd -a 'userName' 'groupName'
  ```

  ```shell
  #临时锁定用户的密码，使账户失效
  passwd 'userName' -l
  #把临时锁定密码的用户开发，使账户重新有效
  passwd 'userName' -u
  ```

  ```shell
  #永久删除用户
  userdel 'userName'
  #永久删除组
  groupdel 'groupName'
  #同时删除用户和组
  usermod -G 'userName' 'groupName'
  #从指定的组中删除指定的用户
  gpassed -d 'userName' 'groupName'
  ```

  ```shell
  #显示用户信息
  id 'userName'
  #将用户添加到指定组
  usermod -G 'groupName' 'userName'
  #修改用户userName1的名称为userName2
  usermod -l 'userName2' 'userName1'
  #锁定账户
  usermod -L 'userName'
  #解除用户锁定
  usermod -U 'userName'
  ```


---

**持续更新中...**