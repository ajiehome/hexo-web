---
title: Java反射对象内容
date: 2023-06-07 15:58:40
tags:
categories: 
- Java基础
---
调用方式

```java
MonthlyCardOrderParam param = new MonthlyCardOrderParam();
        param.setRefundStatus(1);
        notNullCheck(param, NotNull.class, "message");

        notNullCheck(param, NotNull.class, new INotNullParamCheck<NotNull>() {
            @Override
            public void check(String attributeName, Object attributeValue, NotNull annotation) {
                System.out.println(attributeName+"["+attributeValue+"] ->"+annotation.message());
            }
        });
```

---

方法一

```java
/**
     * 判定某个对象的属性值是否为空，通过注解来判定
     * 主要用在非 Controller 上接受参数的时候
     *
     * @param param 所要判定的对象
     * @param annotationClass 所要判定的注解
     * @param func 所执行注解方法的名称
     * @param <E> 判定对象泛型
     * @param <T> 注解方法泛型 Annotation 的实例
     * @throws IllegalAccessException 获取字段的异常
     * @throws NoSuchMethodException 找不到方法异常
     * @throws InvocationTargetException 反射执行方法异常
     */
    public static <E,T extends Annotation> void notNullCheck (E param, Class<T> annotationClass, String func)
            throws IllegalAccessException, NoSuchMethodException, InvocationTargetException {
        // 获取 Field 对象：也就是对象的所有属性对象
        Field[] fields = param.getClass().getDeclaredFields();
        for (Field field : fields) {
            // 设置 private 属性可读
            field.setAccessible(true);
            // 获取当前属性上的某个注解，并且做空值判断
            T annotation = field.getAnnotation(annotationClass);
            if (annotation!=null){
                // 获取当前属性的对应值
                if (field.get(param) == null){
                    if (func!=null){
                        System.out.println(annotationClass.getMethod(func).invoke(annotation));
                    }
                }
            }
        }
    }
```

方法二

```java
/**
     * 校验回调接口
     * @param <T>
     */
    interface INotNullParamCheck<T>{
        /**
         * 校验回调
         *
         * @param attributeName 属性名称
         * @param attributeValue 参数
         * @param annotation 注解对象
         */
        void check(String attributeName,Object attributeValue,T annotation);
    }

    /**
     * 为空校验
     * @param param 校验对象
     * @param annotationClass 查询的接口 Class
     * @param notNullParamCheck 校验回调接口
     * @param <E> 参数对象泛型
     * @param <T> 查询接口泛型
     * @throws IllegalAccessException 找不到属性异常
     */
    public static <E, T extends Annotation> void notNullCheck(E param, Class<T> annotationClass,INotNullParamCheck<T> notNullParamCheck)
            throws IllegalAccessException {
        Field[] fields = param.getClass().getDeclaredFields();
        for (Field field : fields) {
            field.setAccessible(true);
            T annotation = field.getAnnotation(annotationClass);
            Object item = field.get(param);
            if (annotation!=null) {
                System.out.println(item);
                notNullParamCheck.check(field.getName(),item,annotation);
            }
        }
    }
```
