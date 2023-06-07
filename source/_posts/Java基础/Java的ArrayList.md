---
title: Java的ArrayList
date: 2023-06-07 15:58:40
tags:
categories: 
- Java基础
---
# Collection中的List中的ArrayList

`DEFAULT_CAPACITY = 10;`默认大小

ArrayList的初始化操作为，如果你没有传入参数就是赋予一个默认的Object数组对象，然后在添加第一个元素的时候进行扩容，如果我们传入了创建大小的参数，那么会做大小判定，如果为0就赋予一个空的对象，也是一个Object数组，还有如果参数是Collection对象等等情况。

```Java
private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```
在这里有一行重要的代码即使`int newCapacity = oldCapacity + (oldCapacity >> 1);`这里得到的新的容量大小为原大小的1.5倍，是因为使用了向右唯一了一个，相当于除以了2，也就是加上了0.5倍的大小
