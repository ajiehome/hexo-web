---
title: Java的HashMap
date: 2023-06-07 15:58:40
tags:
categories: 
- Java基础
---
# 关于集合中的hashmap

关于tableSizeFor方法：Returns a power of two size for the given target capacity.返回当前数的最小二次幂

默认初始化的hashmap大小为16个大小，默认的负载系数为0.75
负载系数的计算公式：f = 当前实际数目 / hashmap总数目
也就是说当存入的数目大于等于总容量乘以负载系数的时候就会进扩容操作
如果负载系数大于或者等于了规定的负载系数，则会进行hashmap的扩容

get、put：先求hash值，然后右移16位做高位对比，求出hash之后通过hash找到指定的桶，当发生hash冲突的时候，会在同一个桶内存在多个节点，当我们的节点数超过了8个的时候，会做红黑树与链表的转换
```Java
if ((e = p.next) == null) {
 p.next = newNode(hash, key, value, null);
 if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
   treeifyBin(tab, hash); //红黑树转换
 break;
}
```
TREEIFY_THRESHOLD默认值为8，为树型阈值，在8以内使用链表，在8以外使用红黑树

hash表里面所用的hashcode方法中的`Objects.hashCode(key)`，都取自于Object的hashcode方法，而Object的hashcode方法的标注是`public native int hashCode();`，native是java与其他编程语言进行交互的声明保留字段，也就是说这里的hashCode()方法其实调用的是C/C++的hashCode的计算hash值
