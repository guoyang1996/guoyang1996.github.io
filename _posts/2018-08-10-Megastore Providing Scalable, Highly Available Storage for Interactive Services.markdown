---
layout: post
title: Megastore Providing Scalable, Highly Available Storage for Interactive Services
date: 2018-08-10 13:32:20+0300
header-img: mac.jpg
tags: [论文阅读]
---

# 摘要
Megastore 是一个存储系统，为了满足现今的在线交互服务需求。Megastore以一种新的方式，结合了NoSQL数据库的可扩展性以及传统的关系型数据库的便捷性，并且同时提供了强一致性保证及高可用性。我们在细粒度的数据分片上提供完全的ACID保证。这种分片允许我们在一个较宽的网络范围内同步复制写操作，在一个可接受的延迟内，并能够支持数据中心间的无缝故障转移。这篇论文描述了Megastore的语义及复制算法。同时还描述了我们在支持一大堆的谷歌产品服务时的经验。

# 绪论
在线交互服务迫使存储系统满足桌面应用整合到云端的需求。类似邮件、在线文档以及社交网络的服务呈指数型增长，对现有的基础设施带来了极大的考验。

首先，互联网带来了巨量的潜在用户，所以，应用必须考虑到扩展性。应用可以以MySql作为其数据库迅速构建，但是把服务扩展到百万级别用户需要对其存储基础架构一个完全的重新设计。其次，服务为用户而竞争。这需要功能的快速开发以及快速进入市场。第三，服务必须有保证，所以，存储系统必须低延迟。第四，服务必须给用户提供一致性视图--更新的的结果必须立马能看到并且保存下来。看到对一个云传播的页面的编辑消失，不管多么令人信服，都是一个很糟糕的用户体验。最后，用户期待服务是7*24小时的，所以服务必须是高可用的。服务必须能在各种各样的故障中稳定运行，包括个别磁盘错误、机器错误、

这些需求本身是互相矛盾的。关系型数据库提供对快速构建应用的丰富支持，但是很难扩展到百万级别用户。NoSQL数据可如谷歌的 BigTable、Apache的Hbase、Facebook的 Cassandra等都是高度可扩展的，但是仅有有限的API及松一致性给限制了复杂应用的开发。在远距离数据中心间复制数据并提供低延迟具有很大的挑战性，在保证复制数据间的一致性的同时，尤其出现故障时。

Megastore正是为了满足今日的交互性在线鼓舞的需求而开发的存储系统。它的创新点在于：它结合了NoSql数据可的可扩展性以及传统的关系型数据可的便捷性。它使用同步复制来提供高可用性及数据的一致性视图。简言之，它在远距离副本上提供了完全的ACID语义以及低延迟来支持交互性应用。

我们通过在关系型数据库和NoSQL的的一片中间地带完成了这个设计：我们把数据分片存储并分别复制分片，并在分片上提供完全的ACID语义，而在分片之间仅提供弱一致性。我么提供传统的数据库的功能，包括二级索引等，但是仅支持那些能够在用户可容忍延迟内扩展的功能，以及那些我们的分片架构能够支持的语义。我们主张大部分互联网的数据都是可以被合适的分片的（比如按用户），从而使这种算法有限，进一步使得接下来的一些功能特性能减轻开发云应用的负担。

Megastore使用Paxos算法。

Megastore 在谷歌被广泛使用了很多年。每天处理了30亿次的写和200亿次的读事务，并存储了来自全球的数据中心的大概1个PB的数据。

这篇论文的主要贡献在于：

1. 数据模型及存储系统的设计，使得交互性应用能够快速开发并从开始就具有高可用性和高扩展性
2. Paxos复制算法的实现，对远距离分布的数据中间的一致性低延迟优化来为系统提供高可用性的算法
3. 我们的Megastore系统在谷歌的大规模部署的实验报告

# 面向高可用和高扩展

与我们对一个全球的、可靠的、任意大规模的存储平台的需求相反，我们的硬件基础确实受地理位置限制、易于故障、容量有限。我们必须把这些组件组成为一个统一的整体并提供良好的吞吐量和可靠性。

因此，我们实现了两种方法：
	• 为了提高可用性，我们实现了同步的容错的日志复制器，并对远距离连接进行了优化。
	• 为了扩展，我们把数据划分成巨多的小型数据库，每个数据库拥有其自己的可复制的日志，并存储在一个支持副本的NoSQL数据库中

### 复制
为了应对物理灾难，提高云服务的可用性，备份必不可少。
##### 策略
评估以下三种被广泛使用的策略：
	• 主从异步策略 非阻塞性，需要一致性协议。
	• 主从同步策略  阻塞性，主从故障需要外部系统的检测。
	• 乐观复制策略  同一个副本组的成员都可以接受改变，改变在组内异步传播。可用性和延迟都比较优秀。然而，提交时，全局的改变顺序未知，所以事务是不可能的。

我们弃用了故障时可能丢失数据的策略。也丢弃了不支持ACID事务的策略。尽管最终一致的系统具有操作优势，但目前在快速应用程序开发中放弃读取 - 修改 - 写入习惯是非常困难的。

我们也放弃了重量级主节点的选择。需要一系列高延迟操作的故障转移经常导致一个用户可见的中断，并且非常的复杂。如果我们能够完全避开突出的主节点，为什么要构建一个容错系统来实现仲裁和故障转移工作呢？

##### 算法Paxos
我们使用Paxos算法，一种有效的，容错的一致性算法，并且不须强主节点。我们在一个匀称节点组间复制写前日志。任意节点都能够发起读和写。每个日志都附加了来自大多数副本的确认的块，并且少数群集中的副本能够跟上当它们能够时-- 算法的固有容错性消除了对区分“失败”状态的需要。一种对Paxos的扩展，如4.4.1所示，允许对任意最新副本的读操作。另一个扩展支持议论写。

尽管Paxos提供了容错性，使用单一日志依然有局限性。随着副本在一个广泛的领域上传播，通信延迟限制吞吐量。此外，当当前没有复制或大部分写确认都失败时，进度将被阻碍。传统SQL数据库中，使用同步复制日志将导致大范围影响的中断。故为提高可用性和吞吐量，我们使用多重复制日志，每个日志只负责自己的数据分片。

### 分片和位置
为了扩展复制架构和最优化根本的数据存储的性能，我们给应用在他们数据的分片和位置上细粒度的控制。

##### 实体组（Entity Groups）
为了提升吞吐量和局部化故障，我们把诗句分成实体组的集合，每个实体组独立同步的在一篇区域上复制。最底层的数据存储在么个数据中心的一个可扩展的NoSQL数据库中。见图一。
![效果图](../assets/postsimg/mega-1.png)

组内一阶段ACID事务（通过Paxos算法复制）。实体组之间的操作依赖昂贵的两阶段提交，但是一般使用Megastore的高效的异步消息。发送实体组中的事务将一个或多个消息放入队列中; 接收实体组中的事务原子地消耗这些消息并应用随后的改变。
我们在逻辑远距离实体组上使用异步消息机制，而不是物理上远距离的副本之间。数据中心之间的所用网络拥堵都是在复制操作之间的，这些操作是异步且一致的。

一个实体组的本地索引遵从ACID语义；实体组之间仅有松一致性。见图2所示，实体组之间的不同操作。

![效果图](../assets/postsimg/mega-2.png)

##### 选择实体组边界

选择合理的实体组边界非常重要。因为实体组提供了对数据的一种快速操作方式。实体组太小了，导致昂贵的组件操作，实体组太大导致放太多无关数据导致不必要的写，降低吞吐量。

下面的例子展示了应用如何在这些约束下工作的：
Email ：每个邮箱账户为一个天然的实体组
博客：多层实体组。每个用户有一个画像，天然的一个实体组。但是，博客通常是协作的，并且没有永久用户。我们需要实体组来保存博客的提交及元数据。第三个实体组是每个博客独一无二的名字。当一个用户操作同时影响博客和用户画像时，应用依赖异步消息机制。对于一个低拥堵的操作比如创建一个博客并为它命名，两阶段的操作更加方便性能更好。
地图：把地球划分成不相重叠的块，作为实体组。

几乎每个基于Megastore的应用都找到了自己划分实体组边界的方式。

##### 物理布局
我们使用谷歌的Bigtable作为单一数据中心的可扩展的容错存储，使得我们能够支持通过在多行之间传播操作来支持任意读和写。

我们通过让应用控制数据的位置来降低延迟提升吞吐量：在Bigtable的实例之间选择、并指定一个实例的位置。

为了降低延迟，应用视图把数据靠近用户并保持副本之间相互接近。把每个实体组放在它们被访问最多的那个地理区或大陆。在这个区域，他们会放置3-4个数据中心的副本以防物理故障。

为了低延迟、命中效率和吞吐量，每个实体组的数据被保存在Bigtable的邻近范围的行。我们的架构语言能够让应用控制数据的位置，存储数据令其可以在相邻行同时被访问或是非规范化到同一行。

# Megastore 之旅
Megastore将其架构映射到一系列被自习挑选的功能来鼓励可扩展应用的快速开发。这部分讲述最终实现的面向开发者的功能以及我们所做的折中选择。

### API设计哲学
ACID事务简单来源于正确性，但是性能同样重要。Megastore强调成本透明的API，以使得运行时成本与应用开发者的直觉一致。

规范的关系型模式依赖于查询时join来支持用户操作。但这不是Megastore上应用的正确模型，有如下一些原因：
	• 高体量的交互型工作量更多受益于可预测的性能而不是昂贵的查询语言。
	• 读支配写，在我们的目标应用中，所以应该更加关注读的时间而不是写的时间。
	• 存储和查询层级数据在键值对存储中、比如Bigtable中非常的直接

基于此，我们设计的数据模型和架构语言在物理位置上提供细粒度的控制。层级布局和声明的反规范化帮助消除连接需求。查询专门扫描或查找特定表和索引。

连接（join），当需要时，在应用代码中实现。我们提供一个合并阶段合并连接算法的一种实现，其中用户提供返回相同表的以相同顺序排列的主键的多重查询，我们然后返回所有给定查询的键值的连接。

我们也有用并行查询实现外连接的应用。这是典型的使用一个索引查找，并跟着一个并行的索引查找，使用第一个索引查找的结果。我们发现，当第二个索引并行地查找完成时且第一个查找的结果的数量理所当然的小时，这能够很好的替代SQL风格的连接。

尽管schema改变需要对对应查询实现代码的修改，系统依然能够保证，功能能够以一个对性能暗示的清晰理解被构建。比如，当用户（可能没有数据库的背景）发现他们在写一个表示嵌套循环的join的算法时，他们马上能够意识到最好添加一个索引并使用上述基于索引的jion算法。

### 数据模型
Megastore定义了一种介于关系型数据库的元组和NoSQL的精确的行列存储间的数据模型。正如在关系型数据库，数据模型由一个架构声明并且是强类型的。

表要么是entity group root table，要么是child table。
![效果图](../assets/postsimg/mega-3.png)


##### 键上预连接
每个实体被映射成BIgtable的一个单行，主键串联起来构成Bigtable的行主键，其他属性分别占用Bigtable的一列。

user和photo使用同一个user_id，保证顺序，加速join

##### 索引

本地索引 对实体组内部的索引，原子更新，与实体数据保持一致性
全局索引 跨实体组，不保证最近更新
Megastore支持的其他索引功能
	• storing从句：添加额外属性，快速访问
	• 重复索引：PhotosByTag 在重复属性上的索引
	• 内联索引：提取信息碎片，与重复索引组合，还能够建立多对多关系

##### 映射到Bigtable
![效果图](../assets/postsimg/mega-4.png)

在Bigtable行中，存实体组的事务和副本元数据，包括事务日志。

# 事务和并发控制
每个实体组可以看成一个迷你数据库，提供ACID语义。一个事务发起，将把它的改变写入实体组的写前日志，然后将改变应用到数据。

Bigtable允许在同一个行列对中存多个带有不同时间戳的值。使用这种特性来实现多版本并发控制。使最后一次完全更新的事务的时间戳进行读，以防止部分读。读和写不会互相锁，读独立于写事务之外。

Megastore提供当前的、快照的、不一致的读。当前--确定所有的事务已提交；快照--读已提交的事务的读，尽管当前还有事务未提交。不一致--忽略日志状态，允许部分更新数据

写事务开始于当前读。一次性提交所有变化到日志入口，给一个当前最高时间戳，用Paxos追加到日志。该协议使用乐观锁：尽管可能有多个写试图追加日志，仅有一个能够成功。其他失败的将会观察到，并退让，重试。建议锁有助于减少竞争。对特定前端服务器会话的批量写能够避免竞争。

完整的事务的生命周期如下：
![效果图](../assets/postsimg/mega-5.png)

##### 队列
在组间操作时使用，提供事务消息，在单个事务中批量更新，或推迟工作。一个实体组上的事务能够原子性的发送或收到多条消息来更新其实体。每条消息有单一的发送实体组和接收实体组，如果推迟的话，消息的传递是异步的。

队列提供一种对多实体组进行操作的方式。考虑一个日历应用，每个日历是一个独立的实体组，我们想要向其他日历发送邀请。一个简单的事务是原子性的向许多不同的日历应用发送原子消息。每个日历收到消息后将会开启一个自己的事务来处理该条消息，然后将会更新被邀请者的状态并删除该消息。

关系型数据库使用消息队列已经有很长的历史了。我们提供一种新的支持为其扩展性：声明一个队列将原子性的为每个实体组创建一个收件箱，给我们提供百万级别的终端。

##### 两阶段提交

Megastore支持两阶段提交对组间原子性的更新。因为这类事务拥有高得多的延迟并且增加了竞争的风险，我们并不鼓励用户使用这个功能，而是推荐其使用消息队列。但是，这可以简化开发代码。

### 其他功能

全文索引支持、支持断点快照的备份系统、回退进场能够把一个实体组的状态回退到任意一个时间节点，并可选择的删除指定的日志入口（如意外删除以后）。加密数据和事务日志。

# 复制
同步复制架构：一个对Paxos的低延迟的实现。具体细节。

### 概览
Megastore复制系统提供一个单一、一致的存储数据的视图，基于其副本。读、写可以从任意副本开始，并且无论客户端从哪个副本开始，ACID语义都能够得到保证。每个实体组都有副本，通过同步复制该组的事务日志到一系列的副本上。写一般只需一轮数据中心间的通信，健康情况下，读在本地。当前读有如下保证：
![效果图](../assets/postsimg/mega-6.png)

### Paxos的简要总结
Paxos在一组副本之间对单一值达到一致性的一种方法。包容延迟的和乱序的消息以及因为停机导致的复制。但是大多数副本必须活跃且可达，要使算法生效的话。在2F+1个副本中，能够允许F个错误。一旦某个值被大多数所选取，将来的对该值得任意读和写将到达一致的结果。

数据库一般是用Paxos来复制事务日志，其中每个Paxos的实例用于复制日志的每个position。新的值将基于最后一次选出的position来决定写入日志的position。

原生算法不适用于高延迟网络连接，因为它需要多伦通信。 写需要至少两轮组间通信，一轮准备用于获取接下来一轮接受的权利。读需要至少一轮来决定上次被选的值。真实应用需要减少Paxos的轮数，来使之成为一个实际的算法。

### Paxos原生基于主节点的算法
有主节点，总是最新状态，能不须网络通信即可读，写被缩减到一轮基于在每次accept后的对下一次写的准备。主节点能批量写用于提高吞吐量。

依赖于主节点读和写。事务处理必须围绕主节点，来避免接下来读的累积延迟。任何可能的主节点必须用用充足的资源来承载系统的全部负载；从节点将浪费其资源知道其成为主节点。主节点的故障转移需要一个复杂的状态机，并且在服务重新启用是灰耗费大量时间。避免用户可见的中断是很难的。

### Megastore对Paxos的实现

##### 快速读
当前读应该经常在任意副本上都能够执行，不须副本间的远程调用（RPC）。因为写经常在所有副本上都能成功，所以允许在任意地点的本地读是实际的。本地读能够提供更好的利用率、低延迟、细粒度的读容错，以及一个简单的编程体验。

我们设计了一个叫做Coordinator的服务，在每个副本的数据中心服务。调节器服务器追踪一系列的Paxos写已经被观察到的实体组。对该集长得任意实体组，副本有足够的状态来支持本地读。

保持协调者的一致性是写算法的责任。如果对一个副本的写失败了，它就不能提交，直到它的key从该副本的协调者中移除。

协调者非常的简单，所以更可靠、速度更快。

##### 快速写
采用了在基于主节点的算法中的提前准备优化方式。Megastore不是用特定的主节点，而是使用头结点（leaders）。
![效果图](../assets/postsimg/mega-7.png)


一个写操作必须在提交值个其他副本前与leader通信，我们最小化写操作和leader间的通信。我们才用的策略是，从应用提交写最多的区域选取写的leader。这是一种非常简单有效的启发式思想：选最近的副本。

##### 副本类型
至今为止的副本都是全副本，意味着它们包含所有的实体和索引数据并且能够支持当前读。我们也支持见证者副本。见证者参与Paxos的选举轮，并存储写前日志，但是不应用日志也不存实体数据和索引，所以它们的存储成本非常低。用于充人数。

只读副本是见证者的相反，它们不参加选举，但是拥有所有数据的快照。在这些副本上的读能够保证在过去某个时间节点的一个一致性视图。对于能够忍受这种缺陷的的读，只读副本能够帮助在广泛的地理区域内传播数据，而不会影响写入延迟。

### 架构
![效果图](../assets/postsimg/mega-8.png)

### 数据结构和算法

##### 副本日志
允许副本收到乱序提交。
![效果图](../assets/postsimg/mega-9.png)

![效果图](../assets/postsimg/mega-10.png)

##### 读
![效果图](../assets/postsimg/mega-11.png)

##### 写
执行完一次完整的读操作之后，下一个可用的日志位置、最后一次写操作的时间戳， 以及下一次的leader副本都知道了。在提交时刻所有的更新都被打包（Packaged)和提议 (Proposed)，同时还包含一个时间戳、下一次leader提名及下一个日志位置的共识值。如果该值赢得了分布式共识，它将应用到所有的副本中。否则整个事务将中止且从读操作重新开始。

  快速读时协调者的状态是由写算法来保证的。这实际上描述了这样的一个过程：如果一次写操作不是被所有的副本所接受，必须要将这些未接受写操作的副本中相关的实体组从协调者中移去，这个过程称为失效（Invalidation)。失效的过程 以保证协调者所看到的副本上数据都是接受了写操作的最新数据。在一次写操作被提交并准备生效之前，所有的副本必须选择接受或者在协调者中将有关的实体组进行失效。

图是数据写入的完整过程，具体包括如下几个步骤。
![效果图](../assets/postsimg/mega-12.png)

  ⑴接受leader请求leader接受值作为0号提议。这实际上就是前面介绍的快速写方法。如果成功，跳至步骤（3)。

  (2)准备：在所有的副本上使用一个比其当前所见的日志位置更高的提议号进行Paxos准备阶段。将值替换成拥有最高提议号的那个值。

  (3)接受：请求剩余的副本接受该值，如果大多数副本拒绝这个值，返回步骤（2)。

  (4)失效：将不接受值的副本上的协调者进行失效操作。

  (5)生效：将值的更新在尽可能多的副本上生效。如果选择的值和原来提议的有冲突，返回一个冲突错误。


### 协调者可用性
##### 故障探测
##### 生效竞争

### 写吞吐量

### 操作问题

### 产品度量

# 实验和相关工作

# 总结
提出Megastore，一种可扩展的、高可用的数据库，用于满足交互型互联网服务的需求。我们使用Paxos算法进行同步宽域复制，提供轻量但快速的故障转移对独立的操作。使用Bigtable作为扩展存储，用来更多的支持ACID事务、索引、队列。将数据分片成实体组作为子数据库提供了很多事务性的功能，同时还提供了扩展性和吞吐量。

Megastore现已有100多个应用在生产者，面临着内部和外部的用户，提供更高层级的基础架构。这些用用的数量和多样性证明了Megastore的易用性、普适性、和强大。
