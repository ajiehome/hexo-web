---
title: Java动态代理和反射实现字段名栈深寻值
date: 2023-06-07 15:58:40
tags:
categories: 
- Java基础
---
# Java 动态代理与反射实现属性名称栈深寻值

原因：对方法做动态代理，并且动态代理依赖方法形参的某些属性值，而属性值可能是对象的属性的属性，形成一个栈深寻找

思路：
1. 使用Map对所有的形参进行名称和形参对象的映射
2. 把所指定的属性字符串按照指定规则切割载入队列
3. 利用队列出栈的特性，对第一个参数名称做map的 key value 值匹配，找到对应的参数对象
4. 对找到的参数对象做反射解析，利用递归做栈深的属性匹配和值查找
5. 在队列没有匹配字符串的时候，所拿到的值就是我们最终需要的值，直接强转为字符串类型

代码
```java
// 参数对象
class User {
    private String username;
    private String phone;
    private String birthday;

    private User user;
    
    public User(String username, String phone, User user) {
        this.username = username;
        this.phone = phone;
        this.user = user;
    }
// 由于JDK的Proxy代理只能代理接口，所以需要创建一个接口
interface Ts {
    void a(User user);
}
// 执行信息体
class Test implements Ts {
    @Override
    public void a(User user) {
        System.out.println("主要信息执行");
    }
}

// 执行代码
Test test = new Test();
HashMap<String, Object> map = new HashMap<>(10);
LinkedBlockingQueue<String> queue = new LinkedBlockingQueue<>(Arrays.asList("user.user.username".split("\\.")));

Ts ts = (Ts) Proxy.newProxyInstance(Thread.currentThread().getContextClassLoader(),
            Test.class.getInterfaces(), (Object proxy, Method method, Object[] args1) -> {
                // 初始化map
                Parameter[] parameters = method.getParameters();
                for (int i = 0; i < parameters.length; i++) {
                    map.put(parameters[i].getName(), args1[i]);
                }
                // 匹配参数对象
                Object o = map.get(queue.poll());
                // 递归寻值
                while (queue.size() > 0) {
                    o = diGui(queue.poll(), o);
                }
                String result = (String) o;
                System.out.println("lock" + result);
                Object invoke = method.invoke(test, args1);
                System.out.println("unlock" + result);
                map.clear();
                queue.clear();
                return invoke;
            });
    ts.a(new User("huang", "153", new User("Q202111222256489", "158")));
    
// 递归寻值
public static Object diGui(String fieldName, Object o) throws NoSuchFieldException, IllegalAccessException {
    Field field = o.getClass().getDeclaredField(fieldName);
    field.setAccessible(true);
    return field.get(o);
}

```