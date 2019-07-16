---
layout:  post
title:  《java并发编程实战》读书笔记（二）
date:  2019-04-03 14:20:00
tags: [java,读书笔记]
typora-root-url: .
typora-copy-images-to: ..\assets\clipimg
---

# 第5章 构建块

 注：前面有提到过，实现线程安全可以借助已知的一些线程安全的类。所以本章介绍一些java为并发安全提供的一些基础组件。

## 5.2 并发容器

ConcurrentHashMap、ConcurrentLinkedQueue，PriorityQueue（非并发）、BlockingQueue

 

“写入时复制(copy-on-write) ”容器的线程安全性来源于这样-一个事实， 只要有效的不可变对象被正确发布，那么访问它将不再需要更多的同步。在每次需要修改时，它们会创建并重新发布-一个新的容器拷贝，以此来实现可变性。“写入时复制(copy -on-write)”容器的迭代器保留一个底层基础数组(the backing aray)的引用。

 

最常见的生产者消费者设计是将线程池与工作队列相结合。

 

**窃取工作模式：**每一个消费者都有一个自己的双端队列。如果-一个消费者完成了自己双端队列中的全部工作，它可以偷取其他消费者的双端队列中的末尾任务。因为工作者线程并不会竞争-个共享的任务队列，所以窃取工作楼式比传统的生产者消费者设计有更佳的可伸缩性;大多数时候它们访问自己的双端队列，减少竞争。当一个工作者必须要访问另一个队列时，它会从尾部截取，而不是从头部，从而进一步降低对双端队列的争夺。

**例如：**Web Crawler爬取页面。                    

 

## 中断

中断时一种协作机制。一个线程不能够迫使其他线程停止正在做的事情，或者去做其他事情。当线程A中断B时，A仅仅是要求B在达成某个方便停止的关键点时，停止正在做的事情--如果它这样做是正确的。

 

## 同步器（Synchronizer）

阻塞队列、信号量、关卡、闭锁

 

**闭锁：**可以延迟线程的进度直到线程到达终止状态。

 

**CountDownLatch**是一个灵活的闭锁实现，用于上述各种情况;允许一个或多个线程等待一个事件集的发生。闭锁的状态包括一个计数器，初始化为-一个正数，用来表现需要;等待的事件数。countDown 方法对计数器做减操作，表示一个事件已经发生了， 而await方法等待计数器达到零,此时所有需要等待的事件都已发生。如果计数器入口时值为非零,await会一直阻塞直到计数器为零，或者等待线程中断以及超时。

 

**FutureTask**同样可以作为闭锁。(FutureTask 的实现描述了一个抽象的可携带结果的计算 )。FutureTask 的计算是通过Callable实现的，它等价于一个可携带结果的Runnable,并且有3个状态:**等待、运行和完成**。完成包括所有计算以任意的方式结束，包括正常结束、取消和异常。一旦FutureTask进入完成状态，它会永远停止在这个状态上。

 

**计数信号量** (Counting semaphore)用来控制能够同时访问某特定资源的活动的数量，或者同时执行某一给定操作的数量。计数信号量可以用来实现资源池或者给一个容器限定边界。

 

**关卡**( barrier)类似于闭锁,它们都能够阻塞一组线程, 直到某些事件发生。其中关卡与闭锁关键的不同在于，所有线程必须同时到达关卡点，才能继续处理。闭锁等待的是事件;关卡等待的是其他线程。关卡实现的协议，就像--些家庭成员指定商场中的集合地点:“我们每个人6:00在麦当劳见，到了以后不见不散，之后我们再决定接下来做什么。

 

## 总结

- **可变状态**：所有并发问题都归结为如何协调访问并发状态。可变状态越少，保证线程安全就越容易。
- 尽量将城声明为 final类型，除非它们的需要是可变的。
- 不可变对象天生是线程安全的。
- 不可变对象极大地减轻了并发编程的压力。它们简单而且安全，可以在没有锁或者防御性复制的情况下自由地共享。
- 封装使管理复杂度变得更可行。
- 你固然可以用存储于全局变量的数据来写一个线程安全类。但是你为什么要这样做?在对象中装封数据，让它们能够更加容易地保持不变;在对象中封装同步，使它能够更容易地遵守同步策略。
- 用锁来守护每一个可变变量。
- 对同一不变约束中的所有变量都使用相同的锁。
- 在运行复合操作期间持有锁.
- 在非同步的多线程情况下，访问可变变量的程序是存在隐患的。
- 在设计过程中就考虑线程安全。或者在文档中明确地说明它不是线程安全的。
- 文档化你的同步策略。

 