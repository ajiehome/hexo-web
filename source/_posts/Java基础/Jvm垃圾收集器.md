---
title: Jvm垃圾收集器
date: 2023-06-07 15:58:40
tags:
categories: 
- Java基础
---
#  垃圾收集器

新生代垃圾收集器

Serial垃圾收集器：是单线程的收集器，并且在垃圾收集的时候需要暂停所有的用户线程

ParNew垃圾收集器：是Serial收集器的多线程版本，在垃圾收集的时候一样需要暂停所有的用户线程，在多CPU条件下性能比Serial收集器要好，但是由于线程切换需要损耗一定的资源，所以在单CPU的环境下，性能不如Serial好

Parallel Scavenge垃圾收集器：

​	