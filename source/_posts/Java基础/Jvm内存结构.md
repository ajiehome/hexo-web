---
title: Jvm内存结构
date: 2023-06-07 15:58:40
tags:
categories: 
- Java基础
---
# `JVM`内存结构

Java虚拟机分为五个部分：程序计数器、Java虚拟机栈、本地方法栈、堆、方法区

![](https://camo.githubusercontent.com/9ab34e9114814b84731635c2b2cc2abd0084edc44d220602948742aa8ad83340/68747470733a2f2f63646e2e6a7364656c6976722e6e65742f67682f646f6f63732f6a766d406d61696e2f696d616765732f6a766d2d6d656d6f72792d7374727563747572652e6a7067)

**程序计数器（PC寄存器）**

定义：

- 程序计数器是一块较小内存空间，是当前线程执行那条字节码的所在地址，如果当前线程正在执行的是一个本地方法（Native），那么此程序计数器为Undefined。

作用：

- 字节码解读器通过切换程序计数器的顺序，来改变当前执行的指令，从而实现程序的流程控制
- 在多线程的情况下，线程计数器记录的是当前线程的执行位置，当线程切换回来的时候，就知道上次线程的执行位置

特点：

- 是一块较小的空间
- 线程私有，每个线程都有自己的程序计数器
- 生命周期随着线程的诞生而诞生，随着线程的销毁而销毁
- 是唯一一个不会出现`OutOfMemoryError`的内存区域

**Java虚拟机栈（Java栈）**

![](https://camo.githubusercontent.com/109d4eff5b60971060cef6cb1e7923e274771d5e1655e0e5aabaf40df9f9aa6a/68747470733a2f2f63646e2e6a7364656c6976722e6e65742f67682f646f6f63732f6a766d406d61696e2f696d616765732f6a766d2d737461636b2e6a7067)

定义：

- Java虚拟机栈是描述Java方法运行过程的内存模型，Java虚拟机栈会为每一个即将运行的方法创建一块帧栈内存，用于存放该方法运行过程中的一些信息，如局部变量表、操作数栈、动态链接、方法出口信息

