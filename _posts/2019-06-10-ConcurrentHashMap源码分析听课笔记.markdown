---
layout: post
title: 《ConcurrentHashMap源码分析》听课笔记
date: 2019-06-10 13:32:20 +0300
tags: [java,并发,听课笔记]
typora-root-url: .
typora-copy-images-to: ..\assets\clipimg
---
**hashMap的扩容：**通过构建一个新的Map然后把原来的元素插入到新Map中实现。

一个segment就相当于一个HashMap



- ConcurrentHashMap  分段锁 效率高
- HashTable 锁整个对象 效率低



**关键逻辑：**put  与 扩容

 

Java 1.7的实现 数组+链表 分段锁

Java 1.8的实现 数组+链表+红黑树 锁 数组头结点（根节点）位置