---
title: SpringCloud笔记
date: 2023-06-07 15:58:40
tags:
categories: 
- 开源框架
---
# Spring Cloud

**服务注册中心`eureka-server`**

pom支持

```xml
 <dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

application.properties

``` properties
server.port=8080
eureka.instance.hostname=localhost
#是否注册到服务中心，本身就是注册中
eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false
#服务中心的名字地址
eureka.client.service-url.defaultZone=http://${eureka.instance.hostname}:${server.port}/eureka/
spring.application.name=eureka-server
```

启动类支持`@EnableEurekaServer`

```java
@SpringBootApplication
@EnableEurekaServer
public class SubkeyOneApplication {
    public static void main(String[] args) {
        SpringApplication.run(SubkeyOneApplication.class, args);
    }
}
```

访问http://localhost:8080即可看到所有已经注册的服务

---

**服务注册**

pom支持

```xml
 <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
```

application.properties

```properties
server.port=8081
spring.application.name=zuul-server
eureka.client.service-url.defaultZone=http://localhost:8080/eureka
```

启动类支持

```java
@SpringBootApplication
@EnableEurekaClient
@EnableDiscoveryClient
```



---

**负载均衡：`Ribbon`**

在使用`Ribbon`作为负载均衡的时候，需要手动注入一个`RestTemplate`的Bean到Ioc，并且为该Bean加入`@LoadBalanced`开启负载均衡，使其能够以查询到服务注册中心的服务。

例如：

```java
@Bean
@LoadBalanced
public RestTemplate getRestTemplate(){
	return new RestTemplate();
}
```

---

**负载均衡：`Feign`**

使用`Feign`作为负载均衡的时候，需要先在启动类使用`@EnableFeignClients`来开启，而后创建一个接口，使用`@FeignClient(value = "DATA-SERVER")`标签来指定访问的服务

例如：

```java
@FeignClient(value = "DATA-SERVER")
public interface IProductClientFeign {
    /**
     * 请求数据
     * @return 结果
     */
    @GetMapping("/product/all")
    List<Product> allProduct();
}
```

---

**配置服务`ConfigServer`**

pom配置服务包

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```

配置文件`properties`，配置服务参数

```properties
server.port=8030
spring.application.name=config-server
eureka.client.service-url.defaultZone=http://localhost:8080/eureka
spring.cloud.config.label=master
spring.cloud.config.server.git.uri=https://github.com/Huangjie-Github/spring-cloud-config-server
spring.cloud.config.server.git.search-paths=config
spring.cloud.config.server.git.username=Huangjie-Github
spring.cloud.config.server.git.password=h19981228j
```

在启动类上开启服务

```java
@EnableEurekaClient
@EnableConfigServer
@EnableDiscoveryClient
```

---

**使用`ConfigServer`**

pom引入服务使用包

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>
```

配置文件中声明配置服务参数

**注意：**在使用配置服务的时候，下列参数一定要新建一个bootstrap.properties/bootstrap.yml的配置文件，因为bootstrap的配置文件会比application的配置文件先启动加载，需要先启动加载好配置服务才能进行本服务的相关数据注入

```properties
#分支
spring.cloud.config.label=master
#启用的模式
spring.cloud.config.profile=dev
#是否发现启用
spring.cloud.config.discovery.enabled=true
#注册中心的名称
spring.cloud.config.discovery.serviceId=CONFIG-SERVER
eureka.client.service-url.defaultZone=http://localhost:8080/eureka
```

配置之后，只需要在引用的类上使用`@RefreshScope`标签开启，需要引用的参数上使用`@Value`标签即可

---



**消息总线bus：`RabbitMQ`**

pom使用jar

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>   
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency> 
```

配置文件修改

bootstrap.properties

```properties
#与配置服务绑定
spring.cloud.config.label=master
spring.cloud.config.profile=dev
spring.cloud.config.discovery.enabled=true
spring.cloud.config.discovery.serviceId=CONFIG-SERVER
eureka.client.service-url.defaultZone=http://localhost:8080/eureka
#bus总线配置
spring.cloud.bus.enabled=true
spring.cloud.bus.trace.enabled=true
#配置RabbitMQ
spring.rabbitmq.host=127.0.0.1
spring.rabbitmq.port=5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
```

application.properties

```properties
server.port=8011
spring.application.name=feign-server
#路线访问允许/actuator/refresh
management.endpoints.web.exposure.include=*
management.endpoints.web.cors.allowed-origins=*
management.endpoints.web.cors.allowed-methods=*
```

访问需要更新的微服务http://localhost:801/actuator/refresh

该访问不支持Get访问，只支持Post访问

```java
public static void main(String[] args) {
        HashMap<String,String> headers =new HashMap<>();
        headers.put("Content-Type", "application/json; charset=utf-8");
        System.out.println("因为要去git获取，还要刷新config-server, 会比较卡，所以一般会要好几秒才能完成，请耐心等待");
 
        String result = HttpUtil.createPost("http://localhost:8012/actuator/bus-refresh").addHeaders(headers).execute().body();
        System.out.println("result:"+result);
        System.out.println("refresh 完成");
    }
```

---

**断路器`Hystrix`**

pom支持

```xml
<dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>   
```

原来使用Feign做负载均衡的连接接口类修改其

```java
//原来的
@FeignClient(value = "DATA-SERVER")
//修改之后的
@FeignClient(value = "DATA-SERVER",fallback = ProductClientFeignHystrixImpl.class)
```

`fallback`：回退，大概就是说，当指定的微服务不可用时，回退到指定类中的复写方法。因为指定的回退类是继承与当前的接口，那么必定需要继承当前的访问抽象方法。所以，在微服务不可用的时候，调用其复写的方法，做微服务不可用处理。

IProductClientFeign.class

```java
@FeignClient(value = "DATA-SERVER",fallback = ProductClientFeignHystrixImpl.class)
public interface IProductClientFeign {
    /**
     * 请求数据
     * @return 结果
     */
    @GetMapping("/product/all")
    List<Product> allProduct();
}

```

ProductClientFeignHystrixImpl.class

```java
@Component
public class ProductClientFeignHystrixImpl implements IProductClientFeign {
    @Override
    public List<Product> allProduct() {
        ArrayList<Product> products = new ArrayList<>();
        products.add(new Product(1,"数据服务不可用",0));
        return products;
    }
}

```

配置完断路器之后，还需要去启动类中启用它`@EnableHystri`，以及去配置文件中开启相关服务操作`feign.hystrix.enabled=true`

---

**断路器监听`hystrix-dashboard`**

pom支持

```xml
<!--        连接服务注册中心-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
<!--        断路器监听-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
</dependency>
<!--        断路器-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
<!--        环节映射-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

启动类上开启断路器监听

`@EnableHystrixDashboard`：开启断路监听

```java
@SpringBootApplication
@EnableHystrixDashboard
@EnableEurekaClient
public class HystrixMonitorServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(HystrixMonitorServerApplication.class, args);
    }
}
```

application.properties

```properties
server.port=8040
spring.application.name=hystrix-monitor-server
eureka.client.service-url.defaultZone=http://localhost:8080/eureka
```

给共享信息让监听器监听的微服务使用`@EnableCircuitBreaker`开启分享

```java
@SpringBootApplication
@EnableEurekaClient
@EnableFeignClients
@EnableDiscoveryClient
@EnableHystrix
@EnableCircuitBreaker
public class FeignServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(FeignServerApplication.class, args);
    }
}
```

`@SpringBootApplication`：SpeingBoot启动类
`@EnableEurekaClient`：启用微服务注册连接，让注册中心发现，扫描该服务
`@EnableFeignClients`：让注册中心发现这个Feign负载均衡的服务
`@EnableDiscoveryClient`：
`@EnableHystrix`：启用熔断
`@EnableCircuitBreaker`：分享信息给熔断监听，本身必须要有熔断机制

访问地址

http://localhost:8040/hystrix

监听地址

http://localhost:8011/actuator/hystrix.stream

---

断路器聚合监听

pom支持

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-turbine</artifactId>
</dependency>
```

application.properties

```properties
server.port=8050
spring.application.name=hystrix-turbine-server
eureka.client.service-url.defaultZone=http://localhost:8080/eureka

turbine.aggregator.cluster-config=default
#配置Eureka中的serviceId列表，表明监控哪些服务
turbine.app-config=feign-server
turbine.cluster-name-expression= new String("default")
```

启动类开启

```java
@SpringBootApplication
@EnableTurbine
@EnableEurekaClient
public class HystrixTurbineServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(HystrixTurbineServerApplication.class, args);
    }
}
```

使用上面的熔断监听服务去获得监听服务

http://localhost:8040/hystrix、

输入聚合监听的端口地址进行局和监听

http://localhost:8050/turbine.stream

---

**网关`zuul`**

pom支持

```xml
<dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
</dependency>
```

application.properties

```properties
server.port=8060
spring.application.name=zuul-server
eureka.client.service-url.defaultZone=http://localhost:8080/eureka
#映射
zuul.routes.api-a.path=/api-data/**
zuul.routes.api-a.service-id=DATA-SERVER
zuul.routes.api-b.path=/api-feign/**
zuul.routes.api-b.service-id=FEIGN-SERVER
```

启动类开启网关注册`@EnableZuulProxy`

```java
@SpringBootApplication
@EnableEurekaClient
@EnableDiscoveryClient
@EnableZuulProxy
public class ZuulServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ZuulServerApplication.class, args);
    }
}
```

访问地址

http://localhost:8060/api-feign/feign/all

---

