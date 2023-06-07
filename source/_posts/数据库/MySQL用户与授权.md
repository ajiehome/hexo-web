---
title: MySQL用户与授权
date: 2023-06-07 15:58:40
tags:
categories: 
- 数据库
---
# MySQL用户与授权

**1、创建用户**

```shell
create user 'userName'@'host' identified by 'passWord';
```

- `userName`：你需要创建的用户名
- `host`：指定该用户可以在那个主机上可以登录，本地为`localhost`，任意主机可以使用通配符`%`
- `passWord`：指定该用户的登录密码，密码可以为空，

**例如：**

```shell
create user 'admin'@'localhost' identified by '123456';
create user 'admin'@'192.168.1.101' identified by '123456';
create user 'admin'@'%' identified by '123456';
create user 'admin'@'%' identified by '';
create user 'admin'@'%';
```

**2、授权**

```shell
grant 'privileges' on 'database.table' to 'userName'@'host'
```

- `privileges`：用户操作的权限，如`select`，`insert`，`update`等，如果全部需要，使用`all`
- `database`：授权的数据库名
- `table`：授权的数据库表名称，如果对该数据库的所有表都授权可使用`*`通配符

**例如：**

```shell
grant select, insert on test.user TO 'admin'@'%';
grant all on test.* to 'admin'@'%';
grant all on *.* to 'admin'@'%';
```

**注意：**

用以上命令授权的用户不能给其他用户授权，如果需要使该用户可以授权，使用如下命令

```shell
grant 'privileges' on 'database.table' to 'username'@'host' with grant option;
```



**3、设置与更改用户密码**

```shell
set password for 'userName'@'host' = password('passWord');
```

- `userName`：用户的名称
- `host`：用户登录范围
- `passWord`：用户的新密码

**例如：**

```shell
set password for 'admin'@'%' = password("123456");
```

**4、撤销用户权限**

```shell
revoke 'privilege' on 'database.table' from 'username'@'host';
```

- `privilege`, `database`, `table`：同授权部分，参照授权部分

**例如：**

```shell
revoke select on *.* from 'admin'@'%';
```

**注意**

假如你在给用户`'pig'@'%'`授权的时候是这样的（或类似的）：`GRANT SELECT ON test.user TO 'pig'@'%'`，则在使用`REVOKE SELECT ON *.* FROM 'pig'@'%';`命令并不能撤销该用户对test数据库中user表的`SELECT` 操作。相反，如果授权使用的是`GRANT SELECT ON *.* TO 'pig'@'%';`则`REVOKE SELECT ON test.user FROM 'pig'@'%';`命令也不能撤销该用户对test数据库中user表的`Select`权限

具体信息可以用命令`SHOW GRANTS FOR 'pig'@'%';` 查看

**5、删除用户**

```shell
drop user 'username'@'host';
```

