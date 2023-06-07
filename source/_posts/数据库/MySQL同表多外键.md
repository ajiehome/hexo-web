---
title: MySQL同表多外键
date: 2023-06-07 15:58:40
tags:
categories: 
- 数据库
---
# MySQL同表多外键

```sql
drop database if exists test;
create database if not exists test;

use test;

drop table if exists user;

create table  if not exists user(
	user_id bigint(18) comment '用户ID',
	user_name varchar(128) comment '用户名称',
	user_sex int(11) comment '用户性别',
	pass_word varchar(128) comment '用户密码',
	primary key (user_id)
)engine = innodb default charset = utf8;

drop table if exists user_related;

create table if not exists user_related(
	related_id bigint(18) comment '关系ID',
	from_user_id bigint(18) comment '用户ID',
	to_user_id bigint(18) comment '用户ID',
	primary key (related_id),
	foreign key (from_user_id) references user (user_id),
	foreign key (to_user_id) references user (user_id)
)engine = innodb default charset = utf8;


lock tables user write;
insert into user(user_id,user_name,user_sex,pass_word) values (012345678911121314,'用户1',1,'123456');
insert into user(user_id,user_name,user_sex,pass_word) values (012345678911121315,'用户2',1,'123456');
unlock tables;

lock tables user_related write;
insert into user_related (related_id,from_user_id,to_user_id) values (012345678911121313,012345678911121314,012345678911121315);
unlock tables;
```

一个用户表设置多个外键，外键的名字可以不和原表一样，只要类型一致就可
