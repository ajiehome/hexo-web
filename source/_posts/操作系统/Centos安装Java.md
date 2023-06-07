---
title: Centos安装Java
date: 2023-06-07 15:58:40
tags:
categories: 
- 操作系统
---
# Centos使用yum安装java

**首先查询系统是否自带jdk**

```shell
rpm -qa | grep java
rpm -qa | grep jdk
rpm -qa | grep gcj
```

**如果有就卸载对应的**

```shell
rpm -qa | grep java | xargs rpm -e --nodeps
```

**列出所有可安装的rpm包**

```shell
yum list java-1.8*
```

**安装java**

```shell
yum install java-1.8.0-openjdk* -y
```

**校验版本**

```shell
java -version
```

