---
layout: post
title: Online Aggregation and Continuous Query support in MapReduce
date: 2018-06-30 13:32:20+0300
header-img: mac.jpg
tags: [论文阅读]
---
### 一些问题
1. Online Aggregation 是什么？

在线聚合是一种用于改进处理昂贵分析查询的数据库系统的交互行为的技术。几乎所有数据库操作都以批处理模式执行，即用户发出查询并等待数据库处理完整个查询。相反，使用在线聚合，用户在查询发布后立即以在线方式获取聚合查询的估计值。例如，如果最终答案为1000，则在k秒后，用户以95％的概率以[990，1020]的置信区间形式获得估计值。随着系统获得越来越多的样本，这种信心不断缩小。
在线聚合由Hellerstein，Haas和Wang [1]于1997年提出，用于在单个表上进行群组聚合查询。后来，作者展示了如何以在线方式评估联接。[2] 2007年，杰梅因等人。设计并实现了一个名为Database-Online（或DBO）的原型数据库系统，该系统通过在线计算多个表的分组聚合查询，更重要的是以可扩展的方式。[3]所有用于在线聚合的方法都使用随机抽样，由于更新奖励理论的检验悖论，这在分布式环境中是不平凡的。 2011年，Pansare等人。提出了一个贝叶斯模型来处理检查悖论，并实现了类似MapReduce的环境的在线聚合[4]。

2. Continuous Query 指什么？

3. MapReduce 是什么？
MapReduce详解
4. 大数据中的流水线（pipeline）指的是什么？和我们理解的是一个吗？
5. fault tolerance指什么？容错率吗？
6. Map task 、Reduce task?
7. Hadoop。

## 一个支持在线聚合和连续查询的MapReduce框架

MapReduce  使开发者以数据为中心，关注数据集的转换，屏蔽了分布式系统的一些细节（分布执行，网络通信，协作，故障恢复）

作者设计了一个修改过的MapReduce框架，使得数据可以在operators间流水线操作。而传统的MapReduce框架，为了简化故障恢复的设计，每个MapReduce和job的输出是写入磁盘的。

### 流水线（pipeline）MapReduce有如下优点
1. 扩展了MapReduce在批处理间的编程模型
2. 减少完成时间
3. 提高系统对于批处理作业的利用率
在线聚合：可以让用户得到较早的返回值，即边计算边返回。
连续查询：事件监测和流处理

作者搭建了一个称谓HOP的新的MapReduce框架，HOP保留了Hadoop的故障恢复特性，也能够执行用户自定义的原生MapReduce程序。

MapReduce：一般用于面向批处理的大型计算问题，首先考虑的是作业的完成时间。Google的MapReduce框架和开源的Hadoop系统都保留了这个主要功能，他们的批处理实现策略是：每个Map和Reduce的输出在被下一阶段消费或者输出前都被持久化在磁盘上。

批量持久化使得故障恢复非常的优雅简洁。我们知道故障恢复在大型分布式系统中非常的重要，因为节点哟很大概率出现挂机或故障。


### HOP 流水线版本的Hadoop
1. 下游的数据流可以在生产者完成操作前消费数据
2. 通过流水线可以实现在线聚合
3. 支持连续查询


### 流水线机制中存在的challenge
1. 简单的故障恢复机制。Google基于立即持久化数据实现了。我们可以让流水线机制与立即持久化相结合，通过允许生产者向消费者运送数据与持久化操作并行执行。
2. 流水线机制中暗含的频繁通信。这与面向批量操作的“combiners”不同：在通信前执行压缩操作和提前聚合
3. 时间同步机制



### Hadoop框架
1. Hadoop MapReduce
2. HDFS（Hadoop Distributed File System）

### Hadoop MapReduce
1. The Master（The JobTracker）接收作业，分发任务
2. workers（TaskTracker） 处理分发来的任务

## 流水线型MapReduce

### 单个作业间的流水线机制

传统 ： Map->disk->Reduce
修改后的：Map->Reduce

挑战：
	a. 确定粒度（流水线push）。一个简单做法是生成一条record传一条record。通过预处理的聚合来减少网络拥堵。
	b. 排序问题。本来在阻塞型中，在map里面排好。流水线不能这样，解决办法：缓冲，使到达一个阈值，然后进行排序

流量控制机制：map生成一个新的分片时，先查询TaskTracker未发送分片的数量，如果这个值超过了一个指定的阈值（比如假设为2），那么mapper将会缓存多个分片，一旦查询到未发送分片的数量低于这个阈值是，将会把这些分片组合、排序好到一个文件中，然后发送。

combiner流量控制机制：每当调用combiner时，记录下map task输入数据和输出数据的比例，如果combiner能够减小数据量，那么map task 将积累更多的分片。

作业间的流水线机制
	 支持查询结果的提前返回

故障恢复
	Map task failure：先合并，不组合，直到确认提交再组合
	Reduce task failure：在作业完成前，map的输出会同时写入磁盘，以便故障恢复
