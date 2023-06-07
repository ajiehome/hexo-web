---
title: SpringBoot整合Zookeeper简单运用
date: 2023-06-07 15:58:40
tags:
categories: 
- 开源框架
---
---
title: SpringBoot整合Zookeeper简单运用
date: 2022-10-11 10:27:30.04
updated: 2022-10-13 14:43:42.459
url: /archives/spring-boot-zheng-he-zookeeper-jian-dan-yun-yong
categories: 
- Zookeeper
- SpringBoot
tags: 
- SpringBoot
- Zookeeper
---

## 简介
zookeeper一般作为注册中心、配置中心等使用，对于此处的使用，spring cloud zookeeper有非常完善的api，引入相对于的pom即可

而在此处是对zookeeper的基础api进行简单的运用，也就是对zookeeper的节点运用，不涉及集群操作
```xml
<dependency>
  <groupId>org.apache.zookeeper</groupId>
  <artifactId>zookeeper</artifactId>
</dependency>
```

[详情参照](https://zookeeper.apache.org/)

## 节点
zookeeper的节点存储为树形结构，并且其存在多种节点类型，这里只简单使用了持久节点和临时节点
持久节点为客户端一经创建，则会永久的存在与本地文件系统，除非客户端手动删除
临时节点与客户端的本次连接生命周期一致，连接断掉即自动删除节点，也可手动删除

需要注意的是临时节点下不能存在子节点

## 代码使用
属性值
```java
/**
 * @author huangJie
 * @version 1.0v
 * @date 2022/10/10 14:57
 */
@Data
@Configuration
@ConfigurationProperties(prefix = PropertiesPrefixConstant.ZK)
public class ZkProperties {
    private String address;
    private Integer timeout;
}
```
配置: 此处的bean配置的为客户端的连接，Zookeeper的客户端对象作为单例存在于IOC容器，维持客户端连接
```java
/**
 * @author huangJie
 * @version 1.0v
 * @date 2022/10/10 15:06
 */
@Configuration
@ConditionalOnProperty(prefix = Constant.CONDITIONAL, name = "zk", havingValue = "true")
public class ZookeeperConfig {

    private final static Logger LOGGER = LoggerFactory.getLogger(ZookeeperConfig.class);
    @Resource
    private ZkProperties zkProperties;


    public void test(){

    }

    @Bean
    public ZooKeeper zkClient() {
        ZooKeeper zooKeeper = null;
        try {
            final CountDownLatch countDownLatch = new CountDownLatch(1);
            zooKeeper = new ZooKeeper(zkProperties.getAddress(), zkProperties.getTimeout(), new Watcher() {
                @Override
                public void process(WatchedEvent event) {
                    if (Event.KeeperState.SyncConnected == event.getState()) {
                        countDownLatch.countDown();
                    }
                }
            });
            countDownLatch.await();
        } catch (Exception e) {
            LOGGER.error("zk init client for error", e);
        }
        return zooKeeper;
    }

}
```
API提供者
```java
/**
 * @author huangJie
 * @version 1.0v
 * @date 2022/10/10 15:32
 */
@Component
public class ZkProducer {
    private final static Logger LOGGER = LoggerFactory.getLogger(ZkProducer.class);
    @Resource
    private ZooKeeper zkClient;

    /**
     * 创建永久节点
     *
     * @param path 节点路径
     * @param data 节点的数据
     * @return true创建成功 false创建失败
     */
    public boolean createNode(String path, String data) {
        try {
            if (!existsNode(path)) {
                zkClient.create(path, data.getBytes(StandardCharsets
                        .UTF_8), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
            }
            return true;
        } catch (Exception e) {
            LOGGER.error("create zk node for error", e);
        }
        return false;
    }


    /**
     * 创建临时节点
     *
     * @param path 路径
     * @param data 数据
     * @return true创建成功 false创建失败
     */
    public boolean createTmpNode(String path, String data) {
        try {
            if (!existsNode(path)) {
                zkClient.create(path, data.getBytes(StandardCharsets
                        .UTF_8), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL);
            }
            return true;
        } catch (Exception e) {
            LOGGER.error("create zk tmp node for error", e);
        }
        return false;
    }

    /**
     * 更新节点数据
     *
     * @param path 节点路径
     * @param data 数据
     * @return true成功 false失败
     */
    public boolean updateNode(String path, String data) {
        try {
            if (existsNode(path)) {
                zkClient.setData(path, data.getBytes(StandardCharsets.UTF_8), -1);
                return true;
            }
        } catch (Exception e) {
            LOGGER.error("update for zk node data error", e);
        }
        return false;
    }

    /**
     * 删除节点
     *
     * @param path 节点路径
     * @return true成功 false失败
     */
    public boolean deleteNode(String path) {
        try {
            if (existsNode(path)) {
                zkClient.delete(path, -1);
            }
            return true;
        } catch (Exception e) {
            LOGGER.error("delete for zk node error", e);
        }
        return false;
    }

    /**
     * 获取子级节点
     *
     * @param path 节点路径
     * @return 子级结果集合
     */
    public List<String> childrenNode(String path) {
        try {
            return zkClient.getChildren(path, true);
        } catch (Exception e) {
            LOGGER.error("obtain node child for error", e);
        }
        return Collections.emptyList();
    }

    /**
     * 获取节点的数据
     *
     * @param path 节点路径
     * @return 数据
     */
    public String dataGet(String path) {
        try {
            if (existsNode(path)) {
                byte[] data = zkClient.getData(path, true, new Stat());
                return new String(data);
            }
        } catch (Exception e) {
            LOGGER.error("get zk node on data for error", e);
        }
        return null;
    }


    /**
     * 判断节点是不是存在
     *
     * @param path zk节点
     * @return true存在 false不存在
     */
    private boolean existsNode(String path) {
        try {
            return zkClient.exists(path, true) != null;
        } catch (Exception e) {
            LOGGER.error("judge zk node whether exists for error", e);
        }
        return false;
    }

}
```
