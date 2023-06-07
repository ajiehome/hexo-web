---
title: Spring的AOP自定义注解
date: 2023-06-07 15:58:40
tags:
categories: 
- 开源框架
---
# 自定义注解AOP

**POM文件导入**

开启AOP操作

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

**创建一个注解**

元注解

- @Documented：用于描述其它类型的annotation应该被作为被标注的程序成员的公共API，因此可以被例如javadoc此类的工具文档化。它是一个标记注解，没有成员。
- @Inherited：用于表示某个被标注的类型是被继承的。如果一个使用了@Inherited修饰的annotation类型被用于一个class，则这个annotation将被用于该class的子类。
- @Target：用于描述注解的范围，即注解在哪用。它说明了Annotation所修饰的对象范围：Annotation可被用于 packages、types（类、接口、枚举、Annotation类型）、类型成员（方法、构造方法、成员变量、枚举值）、方法参数和本地变量（如循环变量、catch参数）等。取值类型（ElementType）有以下几种：
  - CONSTRUCTOR:用于描述构造器
  - FIELD:用于描述域即类成员变量
  - LOCAL_VARIABLE:用于描述局部变量
  - METHOD:用于描述方法
  - PACKAGE:用于描述包
  - PARAMETER:用于描述参数
  - TYPE:用于描述类、接口(包括注解类型) 或enum声明
  - TYPE_PARAMETER:1.8版本开始，描述类、接口或enum参数的声明
  - TYPE_USE:1.8版本开始，描述一种类、接口或enum的使用声明
- Retention：用于描述注解的生命周期，表示需要在什么级别保存该注解，即保留的时间长短。取值类型（RetentionPolicy）有以下几种：
  - SOURCE:在源文件中有效（即源文件保留）
  - CLASS:在class文件中有效（即class保留）
  - RUNTIME:在运行时有效（即运行时保留）

```java
@Documented
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface AllowToken {}
```

**创建AOP切入实现类**

```java
@Aspect
@Component
public class TokenAspect {
    //设置切入点
    @Pointcut(value = "@annotation(cn.ajiehome.tools.annotations.AllowToken)")
    private void pointCut(){}

    @Before(value = "pointCut()&&@annotation(allowToken)")
    private void before(AllowToken allowToken){
        //AOP前置通知 advice
        //获取被注解方法的request
        ServletRequestAttributes servletRequestAttributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
        assert servletRequestAttributes != null;
        HttpServletRequest request = servletRequestAttributes.getRequest();
        
        String token = request.getHeader("token");
        if (token==null){
            throw new ApplicationException(CodeType.TOKEN_TIME_OUT);
        }
        //解析token
        DecodedJWT decode = JWT.decode(token);
        //Jwt的头部，由于头部被Base64编码过了，所以需要base64逆解编码
        String header = new String(Base64.getDecoder().decode(decode.getHeader()));
        //Jwt的中间部分，理由同上头部
        String payload = new String(Base64.getDecoder().decode(decode.getPayload()));
        //解析头部
        JSONObject userJson = JSONObject.parseObject(header);
        String id = userJson.getString("id");
        String userName = userJson.getString("userName");
        //逆解匹配整个Token的编码以及密钥key
        Algorithm algorithm = Algorithm.HMAC256(id);
        try {
            //设置验证条件，这里设置了验证的密钥和编码以及验证的签发人
            JWTVerifier jwtVerifier = JWT.require(algorithm).withIssuer(userName).build();
            //验证是否匹配，如果Token不匹配或者超时，此处将抛出异常
            DecodedJWT verify = jwtVerifier.verify(token);
            //需要的信息获取从头部份获取和中间部分获取
        }catch (Exception e){

            throw  new ApplicationException(CodeType.TOKEN_TIME_OUT);
        }
    }
    @After(value = "pointCut()&&@annotation(allowToken)")
    private void after(AllowToken allowToken){
        //AOP后置通知 advice
    }

    @AfterReturning(value = "pointCut()&& @annotation(allowToken)",returning = "o")
    private void alterReturn(AllowToken allowToken,Object o){
        //AOP返回通知 advice
    }

    @AfterThrowing(value = "pointCut()&&@annotation(allowToken)")
    private void alterError(AllowToken allowToken){
        //AOP异常通知 advice
    }
}

```

