---
layout: post
title: Approximate Query Processing No Silver Bullet
date: 2018-07-25 13:32:20+0300
header-img: mac.jpg
tags: [论文阅读]
---


# 摘要

在这篇论文，我们对近似查询处理的最新技术进行综述。尽管在这一研究领域取得了很多技术进步，但我们尚未看到它对产品和服务的影响。我们讨论了将 近似查询处理集成到数据平台中的两种有希望的途径。

# 1、绪论

虽然大数据打开了获得前所未有的洞察力的可能性，但其代价是对大量数据上的查询回答的计算资源（或更高延迟的风险）的需求增加。以传统方式执行查询代价的一部分，来提供近似查询的能力，使得我们能够更加高效地探索大数据集。特别地，这些技术能够有效地帮助数据科学家识别那些需要进一步挖掘、发现趋势、进一步可视化的数据的子集。在这篇文章中，我们关注基于采样技术的近似查询处理架构。

一个近似查询处理系统架构能够通过以下一些特性来描述：支持的查询语言、错误模型和准确率保证、运行时工作量的减少以及其在预处理上所增加的额外开销。AQP框架的这些方面不是相互独立的，过去的大部分工作都在这四个方面作出了独特的选择。特别的是，想要构建一个丰富支持SQL的、节省大量工作量的、准确率高到能被大部分应用接受的AQP系统，几乎是不可能的。换句话说，鱼与熊掌不可兼得。

如果真的没有银弹的话，那么人们就想问，AQP是否只是一个不可能的梦想（银弹喻指人们寄予厚望的某种新科技）。事实上，甚至在数十年的研究以后，AQP依然很大程度地限制在学术研究上，并且在今天的产品及服务上依然没有一个很好的范例。由于应用经常使用应用相关的近似，那么AQP的缺少实践不能视为应用对近似值不感兴趣的证据。相反，如果我们查看AQP的近况的话，我们会相信，尽管应用对近似值很感兴趣，近似处理层面的近似值对应用往往无效，因为AQP的误差模型和准确率以及AQP对于工作量的减少都不能够令人满意。

我们坚信AQP议题的价值，插句题外话，在大数据的世界是非常的重要的。我们不应该放弃对类似系统的追求。然而，我们对AQP研究方法的重新思考是有必要的，其目标是使这些系统具有实用性。重新思考的第一步是，清晰的认识到，如何组合AQP的四个维度才能使得应用找到AQP系统的吸引人之处。在这篇文章中，基于我们的反思，我们建议两个值得探求的研究方向。

一个有希望的方向可能是把对精准度的控制权割让给用户。这种方法基于承认AQP系统不会提前（在查询被AQP系统执行完之前）得到任意SQL查询的精确值的事实。在这种情况下，最好是为应用程序员配备语言原语，以帮助他们将近似值注入数据密集型计算中。最后，我们扩展我们的查询余姚到取样器上以使得应用程序员能够表达他们关于对特定程序精准度的语义。我们将把这种方法称为查询时取样并在section2讨论。为了保证程序员有足够强大的操作来在任意SQL查询中使用取样，需要奇特的取样器。比如selection，对一个关系取样能够使接下来的计算成本减少，由于数据量的减少。然而与selection不同，叠加采样器并不总是可行的。因此，引进新的采样器，引进了奇特的基于成本的查询优化挑战--在保证取样器的语义的等价的的一些计划中识别出性能最好的计划。性能的获得依赖于查询计划中取样器的深入程度以及物理访问方法的可用性。注意到这种方式给予程序员在应用中使用关系型语言（SQL及其变种）的全部能力。

我们发现的第二个吸引人的方向是，AQP系统承诺任意到达的查询一个查询无关的精确度的保证。这样一个约定保证了一个统一的方式使得AQP系统对探究性的查询倾斜而不是担心每个查询潜在得不同数量的错误。此时，我们仅能够提供对OLAP形式的查询的支持，而不是所有的SQL，也仅支持一些错误度量值。我们将在section3讨论该部分。关键的直觉是除了使用预先计算的样本之外，还以巧妙的方式（例如，仅针对小的分组）利用索引。与先前几种后验精确度估计（如基于样本算置信区间）的技术不同，我们将展示这些方法如何提供一个前置错误边界。

AQP的工作体量太大，我们不可能一下子全部总结完。因此，我们仅讨论上述两个方向相关的工作，并在section4回顾一些开放性问题。除上述两个方向上的一些技术进步外（以及其他的一些为应用提供了一个清晰的价值议题的算法），更多工作需要做从而使得AQP成为现实。为了提升用户接受度，我们相信在实际的数据平台上实现AQP至关重要。考虑在决策支持查询之下的新的用户场景，以及问自己，近似查询处理如何才能有价值也很重要。这两个议题将在section5进行讨论。

# 2、查询时采样 QUERY-TIME SAMPLING

在查询时采样中，用户明确指定查询中的取样器操作。SQL：2008标准的语法让用户选择取样器的类型、取样比例以及可选择的其他取样器参数。一个查询优化器可以考虑取样器作为计划中的逻辑运算符，并且能选择合适的物理实现（比如水库抽样、从索引中抽样），从基于成本的方式。每个取样运算符有明确的语义，并且查询的输出能够精确地反映用户的说明。查询时取样的工作从[71,75]开始有着丰富的历史。这样取样的优点是，取样器之下的操作能够执行的更快或使用更少的资源。然而，注意到这种方式中，查询执行层只知道如何忠实的执行取样器运算符。对一般的SQL查询，查询系统不会提供统计学精确度保证。早期的工作[71]关注一种取样器运算符，它能够以给定的概率随机均匀选取输入行。许多数据库及大数据系统支持该种均匀抽样运算符。在线聚合方法也是均匀取样。然而，这样做导致了一个更大的挑战，我们将在2.3讨论。我们从讨论均匀取样运算符的几个严重限制开始，这些限制妨碍其通用使用，并以此引进新的取样运算符。

### 2.1 均匀抽样之下  Beyond Uniform Sampling

对于用户来说，想把取样器深入的插入他们的查询中（比如在一个join或一个selection之前）是很自然的，因为这样做能够节省大量的运行时工作。

分组和谓词：考虑在一个关系R的均匀抽样之下执行下列查询
SELECT ZipCode, AVG(Salary) FROM R GROUP BY ZipCode

均匀抽样可能会完全的丢掉某些小的组，或抽取太少样本导致对较小组的平均值估计不准确。类似的问题同样在带有谓词的查询中出现：
SELECT COUNT(∗) FROM R WHERE  County = ‘SanJuan'
当其执行在一个均匀抽样之上。均匀抽样可能会在该谓词上一行也抽不到，或抽取的行数太少以致答案不准确。

一个标准的对上述问题的解决办法是进行有偏抽样对小的分组及满足谓词的行。类似采样的一个运算公式是 distinct sampler：给定参数C,f,p,distinct sampler对列集合C中每个不同的值至少提取f行，并且所有行都能够以至少为p的概率被选取。一个在group-by或谓词列集合之上的distinct sampler将对每个属性列的值产生至少f行样本。

因为区分取样在一个查询流水线中运行时执行，与一般统计学取样的情况不同，区分取样有一些严格的实现要求。任意取样器运算符的理想属性包括一次性完成数据，具有较小的存储器占用空间，并且在运行时不需要在数据分区上并行操作的任务之间进行协调。最近的一篇文章展示了如何完成上述三个属性。

Joins：在典型的数据库系统和大数据系统中，数据在多张表之间传播。因为join的成本比较高，确认先取样后连接能否与先连接后取样有相同的精确度就很有必要了。如果可能的话，那么join可以在取样后进行。

大多数方法只支持多对一的等值连接；如键 外键或多重类似连接在一个star型架构中。在类似连接中，有一张实际表，实际表中的每一行与来自其他维度的表的至多一行匹配（与维度表中谓词同时出现）。因此，这种连接在更宽的行集上镜像谓词。正如上面关于谓词的情况所讨论的，连接列上的事实表的不同样本可以满足这种连接。

对于多对多等值连接，对连接输入进行均匀采样会导致性能和准确性不佳。 当多个输入很大时，仅采样一个输入的性能改善很小。 观察到对于连接输出上的期望采样分数，在连接输入上按下均匀随机采样不是高效的，因为每个连接输入上的采样分数通常需要比连接输出上的期望采样分数大得多。 此外，由于一个输入中的行可以与另一个输入中的多个行匹配，因此获得连接结果的均匀样本还要求每个输入中的行基于它们在其他表中匹配的频率进行采样。 已经提出的用于支持这种相关采样的技术的适用性不会扩展到一般查询，例如当连接的输入是嵌套的SQL语句时。

我们现在考虑哪种采样运算符可以帮助我们有效地对连接采样输入（即，下推采样操作）以获得连接输出的样本。 我们对Universe采样器的建议满足了这一需求。 Universe采样器是均匀采样器的略微松弛，并且具有更高的方差，但在许多实际情况下是吸引人的。 给定参数C，p，Universe采样器随机均匀地选取列集C的值的p分数并输出具有这些值的所有行。 如果多个连接输入在连接列上应用相同的Universe采样器，即它们选择连接列的相同随机值，则universe-sample-then-join与join-then-universe-sample相同。再次注意，连接输出的Universe样本不是均匀随机样本; 它具有更高的方差，可以精确量化。 但是，不仅可以将Universe采样器向下推到连接的输入，它还具有高性能，因为只需要一小部分输入样本来实现连接输出的p分数样本。 早先使用类似的想法估计文档相似性[20]，估计连接基数[81]和清理陈旧观点[62]; 然而，Universe采样器可以更广泛地用于采样一般查询[60]。

### 2.2 取样器上的查询优化 Query Optimization over Samplers

虽然可以根据用户的需要插入上述采样器运算符，但是当这些运算符在计划中深入执行时（例如，连接输入上的Universe采样器而不是对连接输出进行采样），可以获得大的性能提升。 因此，一个自然要问的问题是给定一个用户指定采样器的查询，我们是否可以自动生成更高效的计划，同时保持准确性？

假设采样器被指定为表值函数。 考虑以下重要的查询子表达式类，其中用户计算采样关系上的分组聚合。

SELECT C, Agg 1 , . . . , Agg n
FROM R SAMPLE <samplertype> <samplerparams>
GROUP BY C

这里，C是一组列，Agg i是类似SUM的聚合，最重要的是R可以是由例如嵌套的SQL语句组成的任何通用关系表达式。 请注意，这类查询子表达式比许多AQP技术所支持的要广泛得多。

对于上述类中的任何查询子表达式，如果满足以下条件，则称查询计划转换规则保持准确性：
（1）查询答案中的每个组具有出现在两个计划中的相同可能性。
（2）每个组的总量估计值在两个计划中具有相同的预期值。
（3）转换规则产生的计划与总计估计值的差异不大（与转换前的计划相比）。

对于上述类的查询子表达式，最近的工作表明查询优化器可以提供更高性能的计划，同时提供与用户指定的查询相同的精度[59]。 我们简要介绍下面的方法。

如果仅支持统一采样器，则很容易证明在将采样器推到选择，投影和外键等值线中的事实表之下时，精度得以保留; 几个先前的方法隐含地使用这个属性[10,11]。 如果采样器被推到基表，它们也可以利用物理访问方法（例如，索引，柱状布局）[71]。

但是，当有多个采样器可用时，与查询优化器的交互更有趣。 以下TPC-DS样式查询说明了采样器之间的交互如何帮助下推。
{% highlight sql %}
 SELECT COUNT(*) FROM
(SELECT DISTINCT customer_sk
FROM store_sales, store_returns
 WHERE ss_customer_sk = sr_customer_sk)
SAMPLE UNIFORM 10%
{% endhighlight %}

上述语法意味着要在采样关系上计算COUNT（*）。 由于连接不是关键的外键连接，因此在连接下方推动统一采样器会导致较大的错误（参见§2.1和[34]）。 但是，customer_sk上的10％Universe采样器与统一采样器具有相同的精度。 由于SELECT DISTINCT，我们知道customer_sk的每个不同值恰好在采样器的输入中出现一次。 因此，宇宙采样器在随机均匀地选择10％的customer_sk的不同值在统计上与统一采样器相同，均匀采样器随机均匀地选取10％的行。 现在，可以将Universe采样器推到equi-join下方。这样做可以大大降低成本，尤其是在分发连接时。 宇宙采样器还可以在每个关系中进一步向下推，超过选择，投影，外键等值连接和customer_sk上的任何等值连接; 所有这些都可以进一步降低成本。

为了便于采样器下推，如上例所述，我们需要确定一组本地保持准确性的查询转换规则。 但是，在许多重要的情况下，是否按照数据集推送保留精度的采样器是否会改善计划成本，就像在传统的查询优化中一样。 此外，还有大量的替代计划。 因此，在QO的背景下进行基于成本的探索是恰当的。 以下TPC-DS样式查询说明了这些方面。

{% highlight sql %}
SELECT i_color, SUM(ss_sales)
FROM(SELECT  * FROM store_sales, item
WHERE ss_item_sk = i_item_sk)
SAMPLE DISTINCT {/{i_color/}, 30, 10%}
{% endhighlight %}
这里，用户可能已经使用了不同的采样器来确保每组将采样至少30行（i_color的不同值）。 列ss_item_sk是外键，store_sales比item表大得多。 因此，将采样器推送到store_sales可以提高性能。 但是，由于i_color列没有出现在store_sales中，因此这种下推似乎是不可能的。 但请注意，i_color在功能上依赖于连接列ss_item_sk; 因此将不同的采样器{/{ss_item_sk/}，30％，10％}推送到store_sales上将保持准确性。 然而，这种下推是否提高了性能取决于ss_item_sk的不同值相对于i_color的不同值的数量的数量。 由于前者较大，因此在连接下方推动采样器的增益可能会被较大的样本量所抵消。 因此，需要基于成本的优化来选择适当的计划。

受上述例子的启发，最近的一项工作考虑了查询优化器中采样器之间的相互作用[59]。 它提供了几个本地保持准确性的采样器下推规则。使用这些规则，QO以典型的基于成本的方式探索了许多替代计划。 需要注意的一点是，统一采样器之外的新闻采样器为新的计划转换规则提供了机会; 使用这些规则，QO可以使采样器更深入地进入查询计划，同时保持准确性。 当然，性能提升的程度取决于采样器在查询计划中的深度。

### 2.3 在线聚合

在线聚合是我们上面讨论的查询时间采样方法的一种重要替代方法。 在线聚合方法逐步对输入的前缀执行查询; 它们将结果呈现给用户并在处理更多输入时更新结果。 假设每个前缀是整个输入的统一随机样本，可以为某些查询提供聚合的置信区间。 随着处理更多输入行，置信区间缩小。 在线聚合的关键优势在于用户可以快速收到大致的答案，并且可以在置信水平令人满意时终止查询。

虽然在线聚合具有吸引人的价值主张，但与第2.1节和第2.2节中描述的基于采样器的技术不同，在线聚合需要更多的工作才能集成到现有的数据平台中。首先，他们需要满足条件的物理访问方法，即输入的每个前缀是一个统一的随机样本。如果没有这种支持，随机访问表的I / O成本会很高。维持输入的每个前缀的随机变量的随机样本作为数据演变的不变量也具有挑战性。一些工作解决了物理设计问题[55,56,76]。接下来，在线聚合需要特殊的操作符实现来支持渐进式执行（例如，ripplejoin）[50,57,64]。 Ripple join [50]是一系列运算符，它们可以积极地计算连接，但它需要连接的输入才能保持存储效率。 SMSjoin [54]放宽了这个限制，它将整个连接分成多个rip-ple连接的并集，每个连接都计算在内存中保证输入的部分输入的连接。 DBO系统[57]进一步提高了连接操作员的效率，并展示了如何在输入上控制索引。 WanderJoin [64]将指数用于极值;它表明，给定适当的索引，从输入连接各行的随机步行方法可以产生连接输出的随机样本。但是，这些索引的创建成本可能很高。

# 3、ACCURACY GUARANTEES USING PRECOMPUTED SAMPLES

与第2节中介绍的查询时间采样技术相比，为了更快地缩短响应时间，我们可以在预处理步骤中从数据中提取样本并使用它们来处理传入的查询。 这种直觉几乎不新颖。事实上，基于这一思想开发了很多工作，例如，[11,10,31,17,32,33,72,79,13]。 可以绘制来自数据行的随机样本，并将其实现为数据库中的样本表。 这些预先计算的“小”样本表用于传入查询，从而显着减少查询的运行时间。

如导言中所述，没有银子弹。 实际上，基于预先计算的样本的AQP系统专注于SQL查询的子类。 此类中的查询是一个SQL查询，它包含一个或多个列上的聚合，以及谓词，agroup-by运算符和可能的外键连接。 但是，这种聚合查询在OLAP和许多商业智能应用程序中非常常见且非常重要。

尽管基于预先计算的样本的AQP系统提供低延迟，但是主要缺点是这样的系统通常不提供先验精度保证。 尽管对这些系统所要求的预计算进行了大量投资，但这种缺乏使查询此类系统“命中或错过”。

在3.1节中，我们回顾了一些代表性的过去工作的例子。 在3.2节中，我们回顾了这些系统提供的准确性模型，并解释了为什么它们不足。 在3.3节中，讨论了我们认为有前景的方法 - 使用预先计算的样本以与查询无关的方式提供准确性保证的能力。

### 3.1 Choosing Samples in Pre-processing

预处理和查询时间成本与答案的准确性之间存在权衡。 如何选择样本以及样本表的大小都是该技术在减少查询运行时间方面的准确性和有效性的关键因素。 确定选择包含在样本表中的元组的标准具有挑战性。 考虑带有谓词的临时查询。 可能是在样本表被谓词过滤后，我们可能没有足够多的样本行来准确估计查询的答案。 理想情况下，我们的目标是确保在样本表中评估传入查询中的谓词并执行group-by之后，每个组中保留足够多的样本行，以便估计的答案具有足够小的错误。 类似地，对诸如SUM（A）的聚合的估计的准确性对属性A的异常值是否在样本中是敏感的。

受统计学中的分层抽样技术[65]的启发，AQP系统校准表格不同部分的抽样分数，以创建样本来应对这一挑战。在一些AQP系统中，这种校准完全基于表的模式和统计（工作负载无关），例如[11,10,31,17]。这一工作线由AQUA系统启动[11,10] ，它考虑了分组列的所有可能组合，并为每个组合选择不同的采样分数。还有另一项工作利用历史工作负载分配（工作负载感知），例如[32,33,72,79,13]，假设未来的工作负载与之相同，或者至少具有类似的分布。过去的。例如，在[46]中使用工作负载来构建有偏差的样本。 STRAT [32,33]旨在最小化查询工作负载的预期相对误差。我们现在详细讨论一个用于创建样本表的工作负载独立和工作负载感知技术的示例。

Babcock等人。 [17]提出了小组抽样，一种分层抽样技术，在每一列上构建全局统一样本和偏差样本。 统一抽样在为聚合查询中的大组提供良好估计方面做得很好。 为了提高小组聚合的准确性，对于每列，在列中包含不常值的行包含在有偏差的样本中。 在统一样本和所有偏置样本的并集上执行查询，其中列出现在查询中以估计答案。 这种方法的缺点在于，在实际工作负载中，小组可能由两个或更多列上的约束的交叉所引起，这些约束不能被上述偏置样本集捕获。

BlinkDB [14,13]是一个代表性的工作负载感知AQP系统，如果这些组合（或部分组合）在工作负载中很常见，它可以处理多个列上的小组。 它首先将工作负载信息抽象到查询中出现的一组列（称为QCS），以便它可以对工作负载分布进行有意义的估计。 对于每个可能的QCS，它可以创建一个分层样本 - 在Babcock等人的文章中可以找到类似的分层样本。[17]。 但由于创建和维护所有QCS的分层样本太昂贵，BlinkDB制定了一个优化问题，以决定如何根据以前的工作负载为不同QCS的样本分配空间预算，从而最大限度地减少整个工作负载分布的样本损失。

工作负载感知AQP系统固有的缺点是工作负载可能会不断变化[67]，尤其是对于AQP最有益的探索性工作负载。 在这些系统中，检测工作负荷分布变化和重新优化样品可能是一项昂贵的重复任务。

构建样本表时的另一个目标是确保捕获聚合列上具有不同值的thatrows，如本小节开头所述。 [31]中提出的异常值索引存储每列中具有异常值的行。 它与均匀随机样本一起使用以产生无差异的估计，同时减少SUM和AVG的方差。

### 3.2 Lack of A Priori Accuracy Guarantees

当针对预先计算的样本回答查询时，AQP系统在答案中提供估计的错误是重要的。 有几种方法可以测量以前系统中的错误。 例如，在[11,13]中使用置信区间（CI）。具有90％置信水平的CI = [est-w，est + w]意味着非正式地具有90％（预采样）概率， CI涵盖了真正的答案，其中est是一个组的估计答案[65]。平方误差是[32,17]中使用的另一个误差模型。 Boot-strap [85]用于在答案中提供更严格的错误估计。

无论使用上述哪种错误模型，先前系统的准确性合同中的一个常见问题是它们仅在处理查询后才对错误进行判断，但不能保证错误低于预先规定的阈值。提前做好了。我们以基于CI的错误模型为例。它们可以轻松支持广泛的样本输入和复杂查询。使用例如中心极限定理（CLT）或Hoeffding不等式[47]来计算CI，并且还可以支持不同的聚合函数。但是，CI与数据有关。即，当列A上的值具有大的标准偏差或大的范围MAX（A）-MIN（A）时，具有聚合SUM（A）的查询的结果CI可能比较大。对于使用预先计算的样本的AQP系统来说，这是一个关键问题：在预处理之后使用样本表的大小，错误或CI可以是传入查询的小或大。因此，该系统的有效性是“命中或关闭”。

### 3.3 AQP with Query-Independent AccuracyGuarantee

据我们所知，基于预先计算的样本（包括第3.1节中讨论的那些）的所有先前的AQP系统成功地估计了即时查询的错误，并且在每个传入查询的边界错误（≤ ）中有后验错误。 支持的查询类先验。 为每个查询绑定AQPystem错误的能力使得这样的AQP系统更具吸引力。 在这样的系统上询问查询然后将具有服务级别协议，并且不再显示为“命中或未命中”体验。

对于所有不同的聚合门和误差模型，不能实现这种强有力的保证。 我们将重点关注使用SUM作为聚合函数的查询，以说明先前的方法如何在提供此保证时失败，以及最近的工作Sample + Seek [39]中的关键创新，即为聚合查询的子类提供与查询无关的准确性保证。 以下面的简单查询为例。

SELECT B, SUM(A) FROM T
WHERE C = 10
GROUP BY B

•需要更好的抽样策略。 如果谓词“WHERE C = 10”不是选择性的，即T中的大量行满足它，那么先前的方法如Babcock等人。 [17]和BlinkDB [13]主要依靠均匀随机样本来估计SUM（A）foreach组。 由于每行在样本中具有相同的概率，如果Ais上的分布偏斜，则估计具有大的方差（误差）。 两种方法都可以估计误差，但是对于固定的样本大小，误差可以任意大。

Sample + Seek中采用的采样策略的核心思想是突然出现一个概率与其在A列上的值成比例的行。直觉是，具有接近A的平均值的行可以以比异常值更低的概率被挑选而没有更大的 对SUM（A）的估计结果的影响。 这种策略可以被认为是异常值索引的随机化版本[31]，具有最佳的准确性保证。 它还概括了[9]中COUNT的均匀采样的准确性分析。

•需要索引的帮助。如果谓词“WHERE C = 10”是选​​择性的，即T中只有几行满足它，则全局randomsample不满足，因为它可能不包含这些行。 我们不需要提前估算选择性。相反，我们渠道用预先计算的样本irst处理查询;如果没有足够多的样本行满足谓词，请在[39]中向索引求助。请注意，这些索引可以在大多数数据平台中使用索引的传统实现。这些指示以两种方式使用。在一种方式中，一维索引可以用于检索满足“C = 10”的所有行 - 因为只有少数这样的行，我们可以扫描它们以获得精确的结果。我们将这些指数称为低频指数。此外，anindex可以用于检索行满足“C = 10”的ID的随机样本，其具有行的ID出现在该样本中的特性，其概率与其在A上的值成比例。为了绘制该样本用于多列上的谓词，我们需要计算多个一维指数的索引交集;但是，我们只需要索引交集结果的预测 - 索引可以这样构建，即preix为我们提供了所需的行ID样本。仅对于此小样本中的行ID，我们需要执行索引查找以查找列A和B上的行的值，以便估计每个组的SUM（A）。

Babcock等人的偏见样本。 [17]也可以在上面的第一种方式中使用，但它们不能处理两个或多个列上的选择性谓词，例如“C = 10 AND D = 20”，具有相同的准确性。 BlinkDB [13]中QCS上的分层样本可以处理多个列上的选择性谓词，但存储约束可能会限制可以有效支持的多维QCS的数量。

•可以提供准确性保证。 Sample + Seek中的样品和样品用指数仔细校准，它们可以无缝地覆盖选择性和非选择性查询。 对于COUNT和SUM，Sample + Seek可以在分布精度方面提供与查询无关的准确性保证：即，我们可以对精确和估计的通道进行标准化，使得所有组的标准化值总和为1，并且 分配误差被定义为两个归一化答案之间的L 2距离。 例如，假设示例查询的确切an-swer是x = h69,31i，B上有两个组，并且提供了估计答案x = h70,32i; x中的分布误差isp（69/100 - 70/102））2+（31/100 - 32/102）2≈0.005。 对于受支持类中的任何查询，保证估计答案中的分布误差总是不超过预先指定的误差阈值 ，具有高概率。

可以参考[39]关于如何支持不同类型的预测，例如，多列上的范围约束，以及跨表的外键连接。 还讨论了准确性保证如何推广其他聚合，例如AVG和STD。

将其他误差模型的强精度保证推广到一个开放的问题，例如均方误差。 具有准确性保证的AQP问题的形式化可以如下优化：对于数据库中的用户给定的错误阈值，，预先计算样本和索引，其总大小是数据库大小的函数，支持的查询类，集合 columnsin聚合和谓词，以及错误阈值 ; 这样系统可以确保支持类中的任何传入查询都可以使用预先计算的样本和索引来回答，其中错误最多为 ，概率很高。

### 3.4 Comparison with Query-Time Sampling

与查询时间采样技术相比，基于预先计算的样本的AQP技术实现了响应时间的急剧减少，但是仅限于聚合查询的子类（单块并且仅具有关键的外键连接）。如果分发准确性的概念对于用户来说是令人满意的，则上述的Sample + Seek以独立于查询的方式提供严格且先验的准确性保证。但是，这样的保证需要支持预先计算的样本表和插入。维护各种样本和指示（特别是对于不同的聚合函数和多个聚合列）的累积成本可能导致大量的预计算（和维护）开销。考虑到这种开销，能够压缩这些样本和索引以减少存储开销，同时保持运行时效率是一项技术挑战。另请注意，较大的容错有助于减少这种开销。如果工作负载分布不可预测，Sample + Seek以及以与工作负载无关的方式优化样本的其他方法（例如，Babcock等人[17]）会更好。如果工作负载分布是静态的并且列数不大，则基于历史工作负载优化样本的系统（例如，BlinkDB [14,13]）可能是一个很好的选择。

相比之下，使用§2.1和§2.2中描述的方法的查询时采样不需要预先计算或维护样本表的开销（以及Sample + Seek的索引）。并且，如果查询可以具有嵌套的SQL语句或非关键外键等值连接的连接，则不能使用当前基于预计算的技术，并且查询时间采样仍然是唯一的选择。但是，查询时采样可能具有较小的性能增益，除非采样器可以深入到查询计划中，并且除非在采样器操作符被推送到那些基表时基表上存在物理访问方法。此外，与Sample + Seek不同，查询级别不提供准确性保证。查询时间采样还可以最无缝地与现有数据平台混合，并且可以使用可扩展性机制添加其他采样运算符。但是，在线聚合（查询时采样的变体）具有更高的与现有数据平台集成的工程成本。

# 4、RELATED WORK

在近似查询处理中有如此庞大的工作量。 实际上，对近似查询处理的更广泛的定义还可以包括诸如实体匹配和重复复制之类的技术。 但是，在本文中（以及本节中关于相关工作），我们只考虑从回答查询的角度进行近似查询处理，显着减少工作或延迟，估计答案中的误差很小。

SnappyData [78,5]支持流式，事务性和交互式系统中的近似答案。 它重用了BlinkDB [13]中的许多内容，并在[68]中总结了一些经验教训。 Spark-SQL [6]支持在线聚合和所谓的delta updatequeries以及基于bootstrap的错误界限[84]。 除了这些实例之外，近似查询处理还没有在数据平台中得到广泛应用。

虽然一些研究工作考虑了采样过度流中的问题[19,36,18]，但采样器操作员并没有得到良好支持的语音引擎（例如，Trill [27]，Spark Streaming [83]）具有可启动的异常[5]，如上所述。 采样可能有所帮助 - 因为这些引擎受内存限制，并且与草图不同，示例可以支持更一般的查询。

就先验的错误保证而言，只有COUNT可以由统一或分层的样本支持。 使用测量偏差采样器[39]支持SUM，AVG和STD，GEE [29]可以支持DISTINCT COUNT。 事后可以提供误差的后验估计; 对于COUNT，SUM，AVG和STD以及任何广义单形采样器，可以计算基于后验方差的边界和尾部概率边界[70]; 对于不同的和宇宙采样器也可以提供类似的支持。 处理聚合的随机算术表达式和用户定义的函数一般来说更具挑战性，并且许多问题仍未解决。

为了支持准确性延迟权衡，BlinkDB [13]使用错误延迟证明（ELP）在多个预先计算的样本中进行选择。 查询的ELP记录在样本上执行查询时观察到的准确性和延迟; 它是通过对所有预先计算的样本执行查询来构造的。 他们面临的主要挑战是：当一个新的查询以准确性和性能目标到达时，如何咨询ELP以获得适当的样本（如果有的话）？ 在查询时间采样的上下文中，可以应用采样器下推规则，在本地权衡小的降级精度以获得更好的性能[59]。

为了提高查询优化器内部的成本，样本用于快速估计查询子表达式的基数[81,63,41]。 基数估计类似于没有组的COUNT查询。 因此，即使这些技术与AQP相关，也不要概括为查询答案。

Nirkhiwale等。 [70]展示了如何计算类似SUM的聚合的无偏估计和估计的方差，用于具有多个广义均匀采样器（GUS）算子的计划。 关键的想法是将计划与任意多个采样器转换为在聚合门之前具有单个GUS运算符的新计划，使得两个计划是等效的。 这种转换有助于推导出无偏估计的闭合形式表达式和估计量的方差。 上述见解在[60] toconsider运算符中得到了扩展，这些运算符不是GUS（例如，§2.1中的不同和Universe采样器），并且可以分析丢失组的可能性。对于其他采样器来说，扩展这种分析方法仍然是一个悬而未决的问题（例如， 非GUS，不是明显的而不是宇宙）。 或者，可以在样本上使用自举算子来估计计划精度[12,85,84]。

在本节的其余部分，我们将讨论其他近似查询处理的方法。查看实现[52,15,49]技术预先计算了一些查询的答案，并创建了summariesto，以准确地加速OLAP和决策支持查询（没有近似）。几个引擎使用这些技术的子集[1,8,7]。许多努力减少了高维数据上数据立方体的大小;参见[51]进行调查。类似地，直方图[48]，小波[82,26,48]和草图（下面讨论）可用于近似计算一些聚合;见[47]和[38]这些技术的总结。直觉是，thesedata结构充当原始数据的概要，并且可以大致回答查询的子类。它们的关键优势在于，对于某些查询，这些技术可以为基于采样的系统提供更好的答案（例如，精确或具有小变化的估计）。常见的缺点是它们不能支持一般查询;对于高维数据集而言，它们具有较大的空间开销，并且空间开销变得更大，以支持选择和分组。

草图是一种特定类型的数据概要[38,40,45]，它们对数据流处理产生了巨大影响[69]。 草图特定于特定聚合，并且可以在一个逾越节数据中计算。 一旦计算出来，草图就可以回答相应聚合的查询而无需检查原始输入。因为草图在信息丢失成本方面获得了紧凑性和计算效率，所以在为一个聚合或谓词构建草图之后，草图不能用于支持 查询其他聚合或谓词。

另一项工作[44,43,24,42,22]通过在应用程序引发的基数约束上构建的索引的帮助下，仅通过访问有限数量的数据行来回答特定类型的查询。 BEAS系统[23]对AQP使用了类似的想法。最近的工作[77]提出了一种确定性的近似查询方案。 它使用新颖的位片索引并评估对所有行的前几位的查询作为近似值。 需要研究这种应用方法的有效性。

# 5、 WHERE TO GO FROM HERE?

正如引言中所提到的，尽管有很多技术进步，但近似查询处理还没有成为主流。作为一个社区，我们可以继续进行更多的技术进步，但是在我们看来，我们应该暂停并扪心自问它将使得近似的查询处理成为现实。在本文中，我们认为没有银弹。换句话说，似乎没有单一的技术挑战，解决哪些技术可以实现近似查询处理的潜力。我们认为我们必须问自己，我们可以为一个近似的查询处理系统的用户提供什么明确的价值主张？基于该分析，我们提出了一个双管齐下的方法。一方面，我们应该为应用程序编程器配备查询语言中的重要原语，以便他们可以编写应用程序逻辑来执行应用程序特定的近似。这激发了我们在查询时间采样方面的工作（§2）。另一方面，对于受限制的查询语言，但是适用于商业智能和OLAP查询的查询语言，我们可以使用预先计算的样本和索引（§3）以与查询无关的方式提供准确性保证。通过大幅减少时查询的延迟以及准确性保证，数据分析师可以采用一种新方式与系统进行数据探索查询交互。除了这两种方法之外，我们作为一个社区需要预见替代方案，其中近似查询处理可以为用户提供高价值。

除了为上面讨论的近似查询处理寻求明确的价值主张外，我们还提供以下建议：

将AQP与数据平台集成：可轻松应用于现有数据平台的AQP技术将能够在引擎改进（如矢量化，colum-nar布局和架构趋势）上实现。通过与数据平台的集成，AQP技术将有更大的机会覆盖广泛的用户群。相比之下，独立系统在性能和在广泛使用之前对查询功能的必要支持方面必须满足非常高的标准。数据湖或数据仓库系统为AQP提供了特别有希望的机会。因为在这样的平台上的查询会消耗大量的集群时间（例如，通过点击日志或爬行日志或集群日志记录日志分析管道），即使查询时间采样提供了相对较小的成本降低（例如，50％） ），这些节省的绝对值可能很大。此外，预先计算的样本可用于支持数据库系统中的大型数据集的交互模式;即使查询空间受到限制，用户也可能被低延迟响应和准确性保证所吸引。

查询的近似执行模式：为查询提供近似模式，特别是对于交互式查询，将提供一种有效的方法来安全地试验AQP系统的有效性，从而随着时间的推移克服对接受近似答案的抵抗。我们设想了一种实验模式，每当数据分析师提交即时查询时，系统将同时使用AQP system计算真实答案和答案。如果AQP技术可以快速响应，则结果可以呈现为快速但不精确的全息预览，邀请用户提供两个响应之一：“继续回答完整查询”或“取消完整查询”。这样的设置将允许我们通过获得隐含的用户反馈来更好地评估AQP系统的有效性。理想情况下，这种模式可以针对更有可能受益的查询。与提供不断变化的答案的在线聚合不同，近似模式将仅提供两种可能的答案;一个完全执行查询，另一个来自AQP计划。此外，与在线聚合不同，近似模式不需要新的运算符实现（如第2.3节中所述）。 Suchan评估方法也可用于批量查询，其中AAA可以减少资源使用。

尝试新场景：如所讨论的，对数据的交互式探索可能是AQP最直接的应用。这里，查询可以相对简单（例如，只有select-project-join-groupby操作），并且用户可能不需要精确的答案，但是非常关心极快的响应[21]。另一个例子是生产查询管道;我们在大数据集群中看到许多周期性重复查询，这些查询分析了用于人类消费或可视化仪表板的logsto计算KPI。这些查询的输入是巨大的，例如，点击日志，服务器访问日志。受人类认知和屏幕尺寸限制的输出相当小。因此，此类查询通常具有组和聚合，并且AQP可以降低成本。但是，这些查询也往往是非平凡的（例如，嵌套的SQL语句）。但另一个重要的情况是从后端批处理系统中有效地提取样本的挑战，以便可以重复使用该样本以进行实验。数据分析在他们的桌面上。新方案也带来了不同类型的准确度要求。例如，用户可能只关心结果的排名顺序[61]，或者用户可能只想保留机器学习分类器输出的标签。如何最好地支持AQP的各种准确性要求仍然是一个悬而未决的问题。

近似查询处理以非常有吸引力的价值主张开始。 今天随着数据泛滥，它更具吸引力。但除非我们重新考虑我们的方法来近似查询处理，着眼于场景和明确的价值主张，我们将只有技术结果，而不是可用的系统。

# Acknowledgements

我们非常感谢Christian Konig和Vivek Narasayya阅读本文档的大量版本; 他们的反馈大大改善了这份报告。
