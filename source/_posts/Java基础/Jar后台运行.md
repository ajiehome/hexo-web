---
title: Jar后台运行
date: 2023-06-07 15:58:40
tags:
categories: 
- Java基础
---
# JAR后台运行

保存后台运行，且把日志信息打印输出到指定的文件中

jar name：jar包的名字

log file：完整的日志文件路径，包括日志文件

```shell
nohup java -jar 'jar name' > 'log file' &
```

