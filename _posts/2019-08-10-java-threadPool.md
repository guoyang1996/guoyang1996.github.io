---
layout:  post
title:  java线程池
date:  2019-08-10 14:20:00
tags: [java,并发]
typora-root-url: .
typora-copy-images-to: ..\assets\clipimg
---

## 7个参数

1. corePoolSize：线程池核心线程数量，核心线程不会被回收，即使没有任务执行，也会保持空闲状态。如果线程池中的线程少于此数目，则在执行任务时创建。
2. maximumPoolSize：池允许最大的线程数，当线程数量达到corePoolSize，且workQueue队列塞满任务了之后，继续创建线程。
3. keepAliveTime：超过corePoolSize之后的“临时线程”的存活时间。
4. unit：keepAliveTime的单位。
5. workQueue：当前线程数超过corePoolSize时，新的任务会处在等待状态，并存在workQueue中，BlockingQueue是一个先进先出的阻塞式队列实现，底层实现会涉及Java并发的AQS机制。
6. threadFactory：创建线程的工厂类，通常我们会自定义一个threadFactory设置线程的名称，这样我们就可以知道线程是由哪个工厂类创建的，可以快速定位。
7. handler：线程池执行拒绝策略，当线数量达到maximumPoolSize大小，并且workQueue也已经塞满了任务的情况下，线程池会调用handler拒绝策略来处理请求。

## 四种拒绝策略

1. AbortPolicy：为线程池默认的拒绝策略，该策略直接抛异常处理。
2. DiscardPolicy：直接抛弃不处理。
3. DiscardOldestPolicy：丢弃队列中最老的任务。
4. CallerRunsPolicy：将任务分配给当前执行execute方法线程来处理。

还可以**自定义拒绝策略**。只需要实现RejectedExecutionHandler接口即可，友好的拒绝策略实现有如下：

1. 将数据保存到数据库，待系统空闲时再进行处理
2. 将数据用日志进行记录，后由人工处理

## 不建议使用JDK提供的Executors线程池工具类

JDK为我们提供了Executors线程池工具类，里面有默认的线程池创建策略，大概有以下几种：

1. FixedThreadPool：线程池线程数量固定，即corePoolSize和maximumPoolSize数量一样。
2. SingleThreadPool：单个线程的线程池。
3. CachedThreadPool：初始核心线程数量为0，最大线程数量为Integer.MAX_VALUE，线程空闲时存活时间为60秒，并且它的阻塞队列为SynchronousQueue，它的初始长度为0，这会导致任务每次进来都会创建线程来执行，在线程空闲时，存活时间到了又会释放线程资源。
4. ScheduledThreadPool：创建一个定长的线程池，而且支持定时的以及周期性的任务执行，类似于Timer。

用Executors工具类虽然很方便，依然不推荐大家使用以上默认的线程池创建策略，阿里巴巴开发手册也是强制不允许使用Executors来创建线程池。

Executors工具类无非是把一些特定参数进行了封装，并提供一些方法供我们调用而已，我们并不能灵活地填写参数，策略过于简单，不够友好。

CachedThreadPool和ScheduledThreadPool最大线程数为Integer.MAX_VALUE，如果线程无限地创建，会造成OOM异常。

LinkedBlockingQueue基于链表的FIFO队列，是无界的，默认大小是Integer.MAX_VALUE，因此FixedThreadPool和SingleThreadPool的阻塞队列长度为Integer.MAX_VALUE，如果此时队列被无限地堆积任务，会造成OOM异常。



## 参考文章

1. [你都理解创建线程池的参数吗？](http://objcoding.com/2019/04/11/threadpool-initial-parameters/)