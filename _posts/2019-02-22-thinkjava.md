---
layout:  post
title:  《java编程思想》读书笔记
date:  2019-02-22 14:20:00
typora-root-url: ..
typora-copy-images-to: ..\assets\postsimg
---

- # 第13章 字符串 

-  

- - String对象是不可变的。
  - java编译器在操作String的时，有时会将自动其优化为StringBuilder，但这种优化并不可靠。所以最好显示地使用StringBuilder。
  - StringBuilder是JavaSE5以后引入的，此前用的是StringBuffer。StringBuffer是线程安全的，StringBuilder不是。StringBuffer开销大。

-  

- # 第17章 容器深入研究

-  

- | HashMap           | 推荐使用，插入和查询的开销固定，可以设置容量和负载因子 |
  | ----------------- | ------------------------------------------------------ |
  | LinkedHashMap     | 用链表维护内部秩序                                     |
  | TreeMap           | 红黑树实现，有子树，有顺序                             |
  | WeakHashMap       | 允许释放映射所指向的对象                               |
  | ConcurrentHashMap | 线程安全的Map                                          |
  | IdentityHashMap   | 使用==代替equals()对“键”进行比较的散列映射             |

-  

- - 如果要使用自己定义的类做HashMap的“键”，必须重写其HashCode()和equals()方法。
  - Hashtable、Vector、Stack都是遗留类，最好不要在新的程序中使用。

-  

- # 第21章 并发

- - Thread.yeild()的调用是对线程调度器的一种建议，它在声明“我已经执行完生命周期中最重要的部分了，此刻正是切换给其他任务执行一段时间的大好时机”。

-  

-  

-  

-  

-  

-  

-  