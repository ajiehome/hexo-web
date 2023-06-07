---
title: Redis持久化
date: 2023-06-07 15:58:40
tags:
categories: 
- 数据库
---
# redis的持久化
1. aop（append only file）
    把每一次的数据变化都写入到本地文件 ——— 全持久化模式以日志的形式记录 每一次的写、删操作，以文本的形式记录
2. rdb
    定时把redis中的数据写入到本地文件 ——— 半持久化模式
    fork一个字进程，把数据写入临时文件，然后在替换掉本地的文件，使用二进制压缩
    
## RDB持久化配置

Redis会将数据集的快照dump到dump.rdb文件中。此外，我们也可以通过配置文件来修改Redis服务器dump快照的频率，在打开6379.conf文件之后，我们搜索save，可以看到下面的配置信息：

save 900 1       #在900秒(15分钟)之后，如果至少有1个key发生变化，则dump内存快照。
save 300 10      #在300秒(5分钟)之后，如果至少有10个key发生变化，则dump内存快照。
save 60 10000    #在60秒(1分钟)之后，如果至少有10000个key发生变化，则dump内存快照。

## AOF持久化配置

在Redis的配置文件中存在三种同步方式，它们分别是：

appendfsync always   #每次有数据修改发生时都会写入AOF文件。
appendfsync everysec #每秒钟同步一次，该策略为AOF的缺省策略。
appendfsync no     #从不同步。高效但是数据不会被持久化。