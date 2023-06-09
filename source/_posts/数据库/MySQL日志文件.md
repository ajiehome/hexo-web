---
title: MySQL日志文件
date: 2023-06-09 14:46:09
tags:
categories: 
- 数据库
---
## MySQL日志文件

日志文件：undolog、binlog、redolog

redolog：

- 重做日志，如果在发生故障的时间点（比如系统宕机），尚有数据未写入磁盘，在重启MySQL服务的时候，根据redo log进行重做，从而达保证事务的持久性。

- 基于innodb引擎级别的，在发生故障的时候，innodb会使用到redolog恢复到未发生故障之前，以此来保证数据的完整性。

- 将参数innodb_flush_log_at_tx_commit：设置为0，每次commit先写入到redo log buffer缓存，只有缓存满了才会写入磁盘。设置为1，那么在执行commit时会将redo log同步写到磁盘。设置为2，每次commit写入到page cache。

- 日志空间大小是固定的，通过循环写入的方式将日志写入磁盘，一旦没有空闲空间，则会采用覆盖的方式，覆盖的之前的内容

- 一个文件组，写完之后就覆盖掉

undolog：

- 保证数据的原子性

- 除了记录redo log外，当进行数据修改时还会记录undo log，undo log用于数据的撤回操作，它保留了记录修改前的内容。通过undo log可以实现事务回滚，并且可以根据undo log回溯到某个特定的版本的数据，实现MVCC。

binlog：

- 最核心的一点就是redo log记录的数据变更粒度和binlog的数据变更粒度是不一样的，也正因为这个binlog是没有进行崩溃恢复事务数据的能力的。

- 存储MySQL的所有修改操作（包括DDL和DML等），不包含select、show等查询语句，主要用于恢复数据库和同步数据库。

- binlog 有三种记录格式，分别是ROW（以数据行记录）STATEMENT（以statement形式记录）、MIXED（对能够使用statement的使用statement，不能的则使用row）。

## Java锁

无锁

- 对象无所

偏向锁

- 有一个线程获取当前的锁，锁标记为偏向锁

轻量级

- 当有线程竞争锁的时候，升级为轻量级，此时等待线程进入自旋状态

重量级

- 当有多个线程竞争，或者某个线程自选等待次数过多，升级为重量锁
