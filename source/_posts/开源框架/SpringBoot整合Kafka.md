---
title: SpringBoot整合Kafka
date: 2023-06-07 15:58:40
tags:
categories: 
- 开源框架
---
引入Spring-Kafka的POM依赖

```promql
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
</dependency>
```

配置`application.properties`信息

```properties
spring.kafka.bootstrap-servers=10.254.0.2:9092
spring.kafka.consumer.group-id=multiple-consumer-group
spring.kafka.consumer.auto-offset-reset=earliest
```

配置Kafka的Topic

```java
/**
 * @author huangJie
 * @version 1.0v
 * @date 2022/10/19 9:49
 */
@Configuration
public class KafkaConfig {

    @Bean
    NewTopic multipleTopic() {
        return TopicBuilder.name("multiple-topic").build();
    }

}
```

编写KafkaTemplate使用的Service

```java
/**
 * @author huangJie
 * @version 1.0v
 * @date 2022/10/19 10:01
 */
@Slf4j
@Service
public class KafkaServiceImpl implements KafkaService {

    @Resource
    private KafkaTemplate<String, String> kafkaTemplate;

    @Resource
    private CustomListenableFutureCallback futureCallback;

    @Override
    public void send(String topic, String message) {
        try {
            ListenableFuture<SendResult<String, String>> send = kafkaTemplate.send(topic, message);
            send.addCallback(futureCallback);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

消息发送回调

```java
/**
 * @author huangJie
 * @version 1.0v
 * @date 2022/10/19 14:10
 */
@Slf4j
@Component
public class CustomListenableFutureCallback implements ListenableFutureCallback<SendResult<String, String>> {
    @Override
    public void onFailure(@Nullable Throwable throwable) {
        log.error("kafka send message error", throwable);
    }

    @Override
    public void onSuccess(SendResult<String, String> sendResult) {
        log.info("kafka send message success [{}]", sendResult);
    }
}

```



配置监听

```java
/**
 * @author huangJie
 * @version 1.0v
 * @date 2022/10/19 9:52
 */
@Slf4j
@Component
@KafkaListener(topics = "multiple-topic")
public class MultipleListening {

    @KafkaHandler
    public void handler(String message) {
        log.info("kafka listening message [{}]", message);
    }


    // @KafkaHandler
    public <K, V> void handler0(ConsumerRecord<K, V> record, Acknowledgment ack, @Header(KafkaHeaders.RECEIVED_TOPIC) String topic) {
        log.info("kafka listening message: topic [{}] key [{}] value [{}]", topic, record.key(), record.value());
        ack.acknowledge();
    }

}

```


