---
title: MySQL视图
date: 2023-06-07 15:58:40
tags:
categories: 
- 数据库
---
**1、数据库的查询**

```sql
//选择指定列
select  name, age from demo1;
//定义列的别名
select name as 姓名 ,age as 年龄 from demo1;
//当定义列的别名有空格的时候必须要用引号将标题括起来
select name as "student name" ,age as "student age" from demo1;
//替换查询结果的数据，end后面的字符串代替的是整个case...end语句，不然会直接输出整个case...end语句
select name,age,
  case
    when age is null then "尚未设置"
    when age < 18 then "未成年"
    else "成年"
  end as "是否成年"
  from demo1;
//计算列值，把age扩大10被输出
select name,age*10 as "年龄x10" from demo1;
//消除重复的行
select distinct  name,age,sex from demo1;
```
聚合函数
| 函数名      | 说明                           |
| ----------- | ------------------------------ |
| count       | 求组中的项数，返回int类型整数  |
| max         | 求最大值                       |
| min         | 求最小值                       |
| sum         | 返回表达式中所有值的和         |
| avg         | 求组中值的平均值               |
| std或stddev | 返回给定表达式中所有值的标准值 |
| variance    | 返回给定表达式中所有值的方差   |

```  sql
select count(*) as '一共有多少组'  from demo1;
 select max(age) as '年龄最大的为' from demo1;
select min(age) as '年龄最小的为' from demo1;
select sum(age) as '所有年龄之和' from demo1;
select avg(age) as '平均年龄为' from demo1;
select std(age) as "标准差" from demo1;
select variance(age) as "方差" from demo1;
//返回一组指定列的的所有非null的值，这些值一个接一个的放置，中间用逗号隔开
select group_concat(age) from demo1;
```

| 函数名         | 解释                                                         |
| -------------- | ------------------------------------------------------------ |
| group_concat() | 返回一组指定列的的所有非null的值，这些值一个接一个的放置，中间用逗号隔开 |
| rit_or()       | 与二进制里面的 或 相对应                                     |
| rit_and()      | 与二进制里面的&（与）相对应                                  |
| rit_xor()      | 与二进制里卖弄的^（异或）相对应                              |
