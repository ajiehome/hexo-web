---
title: Seata分布式事务框架
date: 2023-06-07 15:58:40
tags:
categories: 
- 开源框架
---
---
title: 分布式事务框架Seata
date: 2022-03-29 09:30:44.544
updated: 2022-10-13 15:11:45.326
url: /archives/fen-bu-shi-shi-wu-kuang-jia-seata
categories: 
- Seata
tags: 
- Seata
---

基于seata 1.4.2 实现，涉及到的外部文件有：
- server 端的 sql 文件
- client 端的 sql 文件
- nacos-config.sh 执行脚本
- config.txt 执行配置文件
这些文件修改成了，conf下的 REDEME.md / REDEME-zh.md 文件内描述内容，给了所有文件的下载链接，直接查阅该文件即可

---
**开始配置**
[官网参数详情](https://seata.io/zh-cn/docs/user/configurations.html)
[Github上Seata的外部文件地址](https://github.com/seata/seata/blob/develop/script)

server 端
1. 首先在 Github 下载所需版本的 Seata-Server 二进制包，并且在服务器解压
2. 下载数据库文件，这里我们选择MySQL，并且在MySQL数据库中创建一个数据库，并且执行下载下来的数据库文件
3. 修改conf目录下的register.conf文件，首先必要修改的是register部分，register部分为seata-server服务启动之后的注册模式（file 、nacos 、eureka、redis、zk、consul、etcd3、sofa），这里我们选择的是nacos，配置如下：
   ```conf
   registry {
      # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
      type = "nacos"

      nacos {
        application = "seata-server"
        serverAddr = "192.168.246.105:8848"
        group = "SEATA_GROUP"
        namespace = "8b191ab1-8b24-4ea1-be79-5f1668e9046c"
        cluster = "default"
        username = "nacos"
        password = "nacos"
      }
    }
   ```
4. 选贼seata-server服务端的配置文件模式（file、nacos 、apollo、zk、consul、etcd3），如果是选择file就需要同目录下的file.conf文件，作为配置文件，这里我们选择nacos，配置如下：
   ```conf
   config {
      # file、nacos 、apollo、zk、consul、etcd3
      type = "nacos"

      nacos {
        serverAddr = "192.168.246.105:8848"
        namespace = "8b191ab1-8b24-4ea1-be79-5f1668e9046c"
        group = "SEATA_GROUP"
        username = "nacos"
        password = "nacos"
        dataId = "seataServer.properties"
      }
    }
   ```
5. 基于第四步选择的config模式，需要在指定的namespance创建dataId所指定的配置文件，配置文件的内容则是config.txt的内容，修改成需要的数据就好了，下面是我修改的部分，配置如下：
   ```properties
    # 修改事务分组，glcxw_tx_group 是分组，default集群名称
    service.vgroupMapping.glcxw_tx_group=default

    # 选择配置模式
    store.mode=db
    store.lock.mode=db
    store.session.mode=db

    # db的配置
    store.db.datasource=druid
    store.db.dbType=mysql
    store.db.driverClassName=com.mysql.cj.jdbc.Driver
    store.db.url=jdbc:mysql://192.168.246.107:3306/seata?useUnicode=true&rewriteBatchedStatements=true
    store.db.user=glcxw
    store.db.password=glcxw_yyds
   ```
6. 还需要下载nacos-config.sh脚本文件，然后将修改好的config.txt放在脚本的父目录，例如脚本在conf下，config.txt在seata根目录，执行下面的命令，把每一项添加到nacos，为后面client使用
   ```shell
    sh nacos_config.sh -t 8b191ab1-8b24-4ea1-be79-5f1668e9046c -h 192.168.246.105
   ```
7. 在seata-server的bin目录下执行如下命令，把seata-server注册到nacos中，并且指定访问地址和打开的端口
   ```shell
    sh seata-server.sh -h 192.168.246.110 -p 8091
   ```

8. 服务端到此配置完成



client 端

[Nacos 配置中心](https://seata.io/zh-cn/docs/user/configuration/nacos.html)

[Nacos 注册中心](https://seata.io/zh-cn/docs/user/registry/index.html)

首先按照官方的参照文档需要依赖如下文件

```xml
<dependency>
    <groupId>io.seata</groupId>
    <artifactId>seata-spring-boot-starter</artifactId>
    <version>最新版</version>
</dependency>
<dependency>
    <groupId>com.alibaba.nacos</groupId>
    <artifactId>nacos-client</artifactId>
    <version>1.2.0及以上版本</version>
</dependency>
```

这里我们用的是springcloud组合seata，因为`seata-spring-boot-starter`默认带的是1.0.0的seata所以我排除默认的，导入了新的seata 1.4.2

```xml
 <dependency>
	<groupId>com.alibaba.cloud</groupId>
	<artifactId>spring-cloud-starter-alibaba-seata</artifactId>
	<exclusions>
		<exclusion>
			<artifactId>seata-spring-boot-starter</artifactId>
			<groupId>io.seata</groupId>
		</exclusion>
	</exclusions>
</dependency>
<dependency>
	<groupId>io.seata</groupId>
	<artifactId>seata-spring-boot-starter</artifactId>
	<version>1.4.2</version>
</dependency>
```

1. 创建本地数据库中的`undo_log`表，在REDEME-zh.md中有提及

2. 配置yaml文件

   ```yaml
   seata:
    enabled: true
    enable-auto-data-source-proxy: true
    tx-service-group: glcxw_tx_group
    service:
      vgroup-mapping:
        glcxw_tx_group: default
      disable-global-transaction: false
    registry:
      type: nacos
      nacos:
        application: seata-server
        server-addr: 192.168.246.105:8848
        namespace: 8b191ab1-8b24-4ea1-be79-5f1668e9046c
        group: SEATA_GROUP
        username: nacos
        password: nacos
        cluster: default
    config:
      type: nacos
      nacos:
        server-addr: 192.168.246.105:8848
        namespace: 8b191ab1-8b24-4ea1-be79-5f1668e9046c
        group: SEATA_GROUP
        username: nacos
        password: nacos
        data-id: seataServer.properties
   ```

3. 在业务中使用全局事务

   ```java
   @GlobalTransactional(rollbackFor = Exception.class)
   @Override
   public CommonResult<String> create(Integer id) {
       SeataOrder entity = new SeataOrder(1L, 1L, 1L, 1, 1F, 1);
       int insert = baseMapper.insert(entity);
       if (id == 1) throw new RuntimeException("故意抛出");
       return new CommonResult<String>(200, "SUCCESS", "插入成功" + entity.toString());
   }
   ```
4. 如果feign调用的服务使用了全局异常处理，那么处理好的异常会让调用端认为异常已经处理好了，业务为正常不需要回滚全局事务，所以我们需要自定义feign的decoder，在全局异常处理完后对返回的数据进行判断，如果业务失败就抛出一个异常，让全局事务检测到，feign的降级也是如此，decoder代码如下：
   ```java
        
    @Slf4j
    @Configuration
    @Primary
    public class ResultStatusDecoder implements Decoder {
        final Decoder decoder;

        public ResultStatusDecoder(Decoder decoder) {
            this.decoder = decoder;
        }

        @Override
        public Object decode(Response response, Type type) throws IOException, DecodeException, FeignException {
            String resultStr = response.body().toString();
            Integer code = JSONObject.parseObject(resultStr).getInteger("code");
            if (code != 0) {
                throw new RuntimeException("seatA feign rollback");
            }
            return decoder.decode(response.toBuilder().body(resultStr, StandardCharsets.UTF_8).build(), type);
        }
    }

   ```