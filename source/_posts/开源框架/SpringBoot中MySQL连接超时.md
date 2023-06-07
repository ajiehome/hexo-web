---
title: SpringBoot中MySQL连接超时
date: 2023-06-07 15:58:40
tags:
categories: 
- 开源框架
---
# SpringBoot中MySQL链接超时

springboot项目启动开始可以访问数据库，但是几分钟之后就会报错 

```java
Failed to validate connection com.mysql.cj.jdbc.ConnectionImpl
```

由于在使用SpringBoot连接数据库的时候，会建立一个和数据库的连接，这个连接保存在数据库连接池中，而现在这个连接已经Time Out已经不可用了，但是你的连接还是保存在连接池中，SpringBoot还是使用这个连接去连接数据库，所以造成警告信息  

那么知道了原因，我们怎么解决这个问题呢，首先要知道我们可以设置连接在连接池中的存活时间，什么是连接的存活时间呢，简而言之就是你这个连接要保存在连接池中多长时间才被清除，如果你连接时间设置是五分钟，那么这个连接在三分钟的时候timeout了还是得再等两分钟才能被清除  

那么问题就很明了，你的连接在三分钟的时候就已经和数据库断开了连接，但是你默认连接的存活时间是五分钟，所以导致你的springboot项目开始能访问数据库，一会之后就不能访问数据库了  

解决方案
那么这样，假设说连接在三分钟的时候和数据库断开了连接，我们设置连接的存活时间是两分钟，连接就一直处于可用状态，当两分钟之后你再次访问数据库，就会new一个连接，这个连接在其生命周期内仍然是可用的  

```yaml
spring
  datasource
    hikari:
      max-lifetime: 60000 #最大存活时间
      idle-timeout: 60000 #空闲连接时间
      connection-timeout: 50000 #连接超时时间
      maximum-pool-size: 60 #连接池最大数
      minimum-idle: 10 #连接池最小数
```

