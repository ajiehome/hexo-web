---
title: Java反射根据方法名获取参数名
date: 2023-06-07 15:58:40
tags:
categories: 
- Java基础
---
# Java 反射 根据方法名获取参数名

首先实现一个可序列化的Function集成接口

```java
interface CustomFunction<T, R> extends Function<T, R>, Serializable {

}
```

再构建一个Student类，作为本次获取的测试Entity

```java
static class Student implements Serializable {
    private static final long serialVersionUID = -5681957118855943196L;
    private Integer age;
    private String studentName;
    public Student(Integer age, String studentName) {
        this.age = age;
        this.studentName = studentName;
    }
    public void setAge(Integer age) {
        this.age = age;
    }
    public void setStudentName(String studentName) {
        this.studentName = studentName;
    }
    public Integer getAge() {
        return age;
    }
    public String getStudentName() {
        return studentName;
    }
}
```

使用反射获取当前方法的名称，然后切割出对应的属性名称，就是获取get的方法名称，再把get的切割，把第一个字母大写

```java
public static <T, R> String functionToProperStr(CustomFunction<T, R> function) throws Exception {
    Method writeReplace = function.getClass().getDeclaredMethod("writeReplace");
    boolean accessible = writeReplace.isAccessible();
    writeReplace.setAccessible(true);
    SerializedLambda serializedLambda = (SerializedLambda) writeReplace.invoke(function);
    writeReplace.setAccessible(accessible);
    String fieldName = serializedLambda.getImplMethodName().substring("get".length());
    fieldName = fieldName.replaceFirst(String.valueOf(fieldName.charAt(0)), String.valueOf(fieldName.charAt(0)).toLowerCase());
    return fieldName;
}
```