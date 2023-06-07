---
title: Spring静态方法中使用Bean
date: 2023-06-07 15:58:40
tags:
categories: 
- 开源框架
---
---
title: Java 在静态方法中引入bean
date: 2022-04-15 18:27:17.616
updated: 2022-10-13 15:03:27.631
url: /archives/java-zai-jing-tai-fang-fa-zhong-yin-ru-bean
categories: 
- SpringBoot
tags: 
- SpringBoot
---



ApplicationContextAware: 相当于在ApplicationContext上下文初始化的时候，会触发的一个钩子，而要想触发就要加入到Bean容器中去进行管理

```java
public class SpringContextUtils implements ApplicationContextAware {
    private static ApplicationContext applicationContext;

    public static void setStatusApplicationContext(ApplicationContext applicationContext) {
        SpringContextUtils.applicationContext = applicationContext;
    }

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        SpringContextUtils.setStatusApplicationContext(applicationContext);
    }
    public static <T> T bean(Class<T> clazz) {
        return applicationContext.getBean(clazz);
    }
}

```
bean注入
```java
@Bean
public SpringContextUtils springContextUtils(){
    return new SpringContextUtils();
}
```
而后只需要在使用的时候使用bean方法，依据类型或者名称等等进行注入即可