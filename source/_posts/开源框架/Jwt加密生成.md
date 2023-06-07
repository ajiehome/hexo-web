---
title: Jwt加密生成
date: 2023-06-07 15:58:40
tags:
categories: 
- 开源框架
---
# JWT初步使用

**POM文件导入**

```xml
<dependency>
    <groupId>com.auth0</groupId>
    <artifactId>java-jwt</artifactId>
    <version>3.10.1</version>
</dependency>
```



**JWT实现类**

```java
@Component
public class JwtUtils {


    /**
     *  获取Token，签名由ID代替，形成动态签名
     * @param autoDuration 超时的时间长度
     * @param jwtBeanBO 用户数据
     * @return token
     */
    public String getToken(long autoDuration, JwtBeanBO jwtBeanBO){
		
        long outLong = System.currentTimeMillis() + autoDuration;
        Date outDate = new Date(outLong);
        //加密方式HS256
        Algorithm algorithm = Algorithm.HMAC256(jwtBeanBO.getId().toString());
        HashMap<String, Object> hashMap = new HashMap<>(5);
        hashMap.put("id",jwtBeanBO.getId());
        hashMap.put("userName",jwtBeanBO.getUserName());
        hashMap.put("passWord",jwtBeanBO.getPassWord());
        hashMap.put("userPhone",jwtBeanBO.getUserPhone());
        hashMap.put("userAddress",jwtBeanBO.getUserAddress());

        return JWT.create()
                .withHeader(hashMap)
                .withIssuer(jwtBeanBO.getUserName())
                .withExpiresAt(outDate)
                .sign(algorithm);
    }
}
```

**具体操作见:[Jwt-Github](https://github.com/auth0/java-jwt)**