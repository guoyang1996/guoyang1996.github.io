---
layout: post
title: Social Learning Networks A Brief Survey 
date: 2019-02-26 13:32:20+0300
tags: [论文阅读]
typora-root-url: .
typora-copy-images-to: ..\assets\clipimg
---

# 摘要

社交学习网络是一种在学生、指导者及学习模型之间形成的网络。它由表示学习者和学习过程间行为构成的图之间的动态学习行为组成。近年来的在线教育上的一些发明，包括不同层次的开放在线课程资源，在翻转课堂教学中，在专业培训和企业培训中，都提出了有关SLN的有趣问题。收集、分析和利用有关SLN的数据可以得到这些问题的潜在答案，借助建模语言和设计方法的融合，如社会网络理论、学习科学和教育信息技术。本文概述了在这个新兴研究领域中的一些主题，包括预测、推荐和个性化。

 

# 绪论

社交学习网络(Social Learning Network, SLN)封装了各种各样的场景，其中许多人通过交互相互学习。SLN模型包括功能模型和图论模型。一般来说，SLN中的节点表示学习者、学习概念，或者同时表示学习者和概念。连线将表示这些节点之间的联系接，可以无向表示相似性，也可以定向表示信息流。这些连线还可以通过加权来赋予这些意义以及大小，这些意义通常是从一些网络测量中提取的。

## A 动机和应用

1）MOOC：MOOC的快速发展促成了全球学习之间基于教育目的的联系。Edx等平台成为了人们获取高等教育的主要平台。

这些平台有以下特点：在线、免费、开放内容、学生很多，低完成率，带有测验的课程视屏，以及对于SLN最重要的一点--讨论区，也就是学生与学生间及学生与老师之间交互的首选方式。

MOOC讨论区类似于Q&A网站，但是有不同的意义。一般来说，每个课程讨论区由一些线程组成，每个线程都由很多提交。第一个提交的人就是该线程的创造者。然后一次提交可能会反过来包含许多评论，从而构成了一个三层结构（线程-提交-评论）。用户在一个讨论区可以新建一个线程或提交一次答案，或在一个已有的评论上进行评论。当创建一个线程的时候，每个课程都有许多论坛目录可以使用，但是经常会出现前后矛盾的情况么因为并没有有效的机制促使学习者维护该一致性。在MOOC平台上的提交和评论能够获得来自用户和维护者的支持与反对。

2）FLIP（翻转课堂）：混合教育，结合在线教育和传统教育，在线看视频，课堂时间作为讨论时间。学生在翻转课堂讨论区的交互使我们能够提取出SLN。这里的提交和评论类似于MOOC中的，但是我们期望提取出的网络能够有一些不同：

（a）大小和稀疏性：翻转课堂学习者的数量更小，但是他们之间的联系更加密集。这是因为MOOC上的学习者的背景更加不同，并且讨论区的参与并不会有益于学习者在课程上的表现。在翻转课堂上，学生有更高的概率遇见彼此，并且更容易交互。

（b）信息讨论与对话讨论。除了与课程相关的信息讨论，MOOC论坛往往还包含大量的对话讨论，学习者可以在其中进行“闲聊”。在翻转课堂中，SLN通常不包含对话讨论，原因有二:第一，学生可以很容易地面对面或通过其他媒体进行非正式的交流;第二，老师的密切监控可能会阻止这种行为。

3） Q&A 网站：许多流行的社交问答门户网站已经出现，它们通过便捷的搜索引擎允许用户以自然语言的形式输入问题。今天，像雅虎这样的网站!

 Answers、WikiAnswers和Stack Overflow每天都接收数千个问题，其中包含来自不同主题领域的材料。比如，Stack Overflow自2008年成立以来，已经积累了280万注册用户和670万个问题。

与大多数在线论坛一样，问答网站通常允许用户使用一些关键功能:发布问题、回答问题、对答案进行评论和投赞成票/反对票。此外，他们通常允许提问者选择一个“最佳”或“可接受”的答案。同样，作为一种质量保证和激励机制，Q&A门户通常会将分数分配给接受正面投票的用户，同样也会将分数分配给接受负面投票的用户。

我们可以识别出问答网站的SLN和在教育环境中形成的SLN之间的几个不同之处：

（a）激励结构：问答网站有一套定义良好的、自动的激励机制来鼓励参与。使用MOOC和FLIP，教师可以选择自己设计这些，但这可能不是一个可扩展的过程。像Coursera这样的平台已经开始提供学生论坛排名，但它并不与学生的表现挂钩。

（b）更广泛的概念列表：在SLN中传播的课程信息将仅限于与课程相关的材料。每个课程只有一个论坛，只有注册的学生可以参加。虽然一些问答论坛有一些特定的焦点，但我们预计这些SLN中出现的概念的数量会更加广泛。

（c）单一的学习方式：在问答论坛中，SLN是学习的唯一方法，而在教育环境中，它只是四种模式之一:还有讲课视频、评价和文本资源。

 

## B 图类型

 

1）学习者之间的无向图：如果我们将SLN中的学习者视为节点，我们可以使用无向连接来表示他们之间是否存在某些特征。这些属性可以是年龄、地理位置、教育水平、互相之间是否有交互，等等。对于结点i和j，我们可以这样说：

![公式](/../assets/clipimg/clip_image001-1560925546144.png)

其中 propk (i, j) 是一个二进制变量，其中1表示i和j满足属性集K中的属性k（比如年龄在18到24之间）。换句话说，节点i和j有联系当且仅当它们满足至少中P个属性相同。

 

2）学习者之间的有向图：除此之外，我们可能想要测量SLN中的信息流。因此，我们可以根据互动讨论区，分析学习者之间的关系。通过这样，比如，当j回答了一个i回答的问题时，我们可以在i和j之间画一个有向连线。这可以进一步限制为对给定问题的“最佳答案”，也可以是一个多图，其中i和j之间有多个链接，因为学习者可以问和回答多个问题。

 

3）学习者与概念之间的无向图：我们还可能对将信息表示为二型节点感兴趣。关键概念可以通过多种方式提取，例如通过文本分析来查找讨论中的关键字，或者使用教师指定的教学大纲，然后我们可以定义学习者和概念之间的双向图。为了表示这个，我们同样可以用公式（1），但是此时，每个属性表示用户i参与了概念 

 

4）学习者与概念之间的有向图：最后，我们可能希望更详细地描述交互的结构。要做到这一点，我们可以再次合并概念节点，除了这里我们将把学习者提出的每个问题或帖子视为一个单独的问题。这样，我们为每个帖子定义了两组链接:（i0，j）∈G表示学习者i0首次发表该帖子，（j，il）∈G鸟事学习者il在帖子下发表了评论。在讨论区里，用户可以对每个帖子或评论点赞或点踩，我们可以为这些连线用获得的赞同数进行加权。

 

这四个示例旨在说明一些可以在SLN上定义的有用的图，这些图并不全面。此外，图结构上的动态功能也是模型的一个组成部分。

 

# 研究问题

 

## A 预测

1） 表现：预测学生在评估（即家庭作业、测验或考试中没回答的题）中的表现的能力 ，是一个重要的研究领域。解决这个问题的一种自然的方法是使用一个分类模型，在这个模型中，每个观察值代表一个用户在评估中的分数。因子分析技术最近发展起来，用于基于传统课堂的数据提取 问题-概念间关系及用户熟练程度。在MOOC环境中，我们可以访问更多用户的数据，但通常每个学生回答的问题数量会更少，从而导致稀疏性问题。例如，在我们的N:FMB MOOC中，在回答至少一个问题的学生中，回答问题的平均比例只有9%。同时，在MOOC上我们可以访问大量的SLN数据，对这些数据进行分析可以提高使用传统技术的准确性。

2)辍学率：另一个应用，尤其是对于在线课程，是预测辍学率。这可以针对个别学生，也可以针对整个课程的参与量。感兴趣的指标可以是特定作业的完成情况、讲课视频或给定的讨论参与率。最近，[13]专注于预测MOOC中作业的完成情况，方法是在每个作业中为不同的学习者组添加一个自定义接口。

SLN数据可以帮助检测学生的退学情况，并随后修改课程以增加留校率。例如，如果一个学生通常积极提问或回答问题，那么如果她的参与率下降，这可能是一个预先的迹象，表明她不会完成指定的任务。即使是在论坛上不活跃的学习者，利用相似用户的SLN信息也可以帮助做出预测。

 

 

## B 推荐

利用SLN的推荐方法在许多场景中也很有用。尤其是MOOC，信息泛滥，学习者众多。

1）课程和话题：这是一个比仅MOOC广阔得多的市场，在线教育的一般市场提供大量的课程，每个课程通常都有丰富的教材和大量的论坛学生活动。学生往往很难找到最相关的信息，以满足他们的个人学习欲望和需要。这带来了一个通过设计智能算法来帮助学生导航来改善教育体验的机会。在一个给定的平台上，一个学生将看到一个她可能不知道存在的潜在兴趣的课程列表。在一门给定的课程中，学生将被告知哪些授课视频是最重要的(考虑到他的先决条件)，并将被重定向到最相关/最有趣的讨论主题。

2）学习伙伴：对于许多学习者来说，与学习伙伴的协作是参与的一个重要方面，无论课程是否在线。在任意类型的MOOC中，都有成千上万来自不同背景的学生，因此，对于任何一个给定的学生来说，都可能有许多其他的学生是她学习的“合适人选”。与此同时，这些庞大的数字——由于学习的异步性和缺乏面对面交流而进一步复杂化——使得学生很难自己找到并形成这些最优分组。例如，以图1为例，它描述了来自我们的N:FMB MOOC的论坛的SLN。从图(b)可以看出，该图是高度不连通的，这表明学生没有在他们的SLN中探索许多潜在的连接。

推荐算法可以关注相似点，或者更重要的是，用户之间的不同之处。例如，一个积极参与某些主题论坛的学生可以与另一个在这些主题上苦苦挣扎的学生配对。

![ Forum discussion.](/../assets/clipimg/clip_image002-1560925436153.png)

3）学生互评：在大规模的在线课程中，师生比例非常小，通常不到1%(例如，考虑到在论坛上至少发布过一次[2]的人，我们观察到73门课程的平均比例为0.0035)。因此，教师手工给每个学生的作业打分是不可行的。目前一种避免这种情况的方法是给学生布置可分级的作业和考试，比如多项选择题。但这常常会降低问题的质量。

在很多情况下，MOOC的导师们转向了另一种选择，要求学生们互相打分。到目前为止，这种分级制度缺乏有效性，主要有两个原因。首先，不同的学生有不同的评分质量:与任何课程一样，MOOC上的学生对材料的理解也不同。其次是评分所需的时间投入:老师通常希望每个参与者至少批改一些作业，而一些学生可能无法或不愿意做出必要的时间投入。[14]最近开发了一种方法，通过对学习者属性的统计估计，确定MOOC中哪些提交内容需要分配额外的评分。但是，应该给学生分配哪些任务的问题还没有得到探索。可以使用SLN的结构来发现每个作业主题范围的大V。此外，希望设计一个系统来抵消评分偏差，并使用统计估计来减少每个学生所需的工作总量。

 

## C 个性化

关于在线教育的一个基本争论是学习效率和学习规模之间的权衡。如图2所示，它显示了各种mooc的经验注册-完成数据对，很少有超过10%的学生完成其中一门课程。

  ![100  Classroom  10  Our Goal  Best MOOC  100  Today  150  250  Enrollment ](/../assets/clipimg/clip_image001-1560925773960.png)

MOOC尚未奏效的原因有很多:师生比例非常低，学习是异步进行的，学生群体非常多样化。课程个性化需要先进的技术，以提升这一权衡曲线，使大规模有效学习成为可能。与拥有一个通用的在线课程不同，教师应该能够编写并交付一个自适应的版本，实时更改内容，以满足每个学生的需求。

学生在SLN上的行为是这种适应的一个未被充分研究但潜在的关键输入。反过来，个性化将为学生提供一个有效的途径，形成与那些在相同或类似的学习路径的人的联系。我们将概述学习系统中的个性化引擎如何与SLN研究相互作用。

 

现有的自适应教育系统有很多。它们中的大多数都仅限于web浏览器，并且支持作为课堂教学补充的内容。无论应用程序是什么，大多数个性化框架都将在反馈循环中包含三个顺序的组件，如图3所示。



![ Fig. 3: A typical flowchart of individualization. Each block  constitutes a substantial engineering challenge in its own right. ](/../assets/clipimg/clip_image001-1560925862279.png)



仍然具有挑战性的是设计、实现和部署这样的反馈系统，以及如何将SLN集成到体系结构中。我们在下面的设计中概述了一些关键元素。

 

行为测量：这个功能模块包括用户与课程材料交互时行为的度量。例如，可以捕捉到用户的视频观看轨迹(带有暂停和跳转)，以及她在测试和其他评估中的答案。最值得注意的是，用户在与SLN交互时在论坛中输入的信息也可以收集。如此丰富的信息集可用于生成适应所需的数据分析，并支持机器学习、数据挖掘和社交网络分析中的许多研究路径。

 

数据分析：该模块利用机器学习技术生成高维学习过程的低维模型。潜在空间可以是(a)通过数据挖掘发现的，也可以是(b)根据作者指定的学习特征预先定义的。对(a)，它代表着朝着迄今仍难以实现的目标迈出的重要一步:对学习过程的结构化理解。对于(b)，教师可以灵活地决定特征的数量和名称。正如[16]中定义的，它们可以表示用户“目标、知识、背景、超空间体验和偏好”。教师将用与之相关的特性标记内容的片段，每个学生在每个特性维度中的模型都可以使用这些特性进行更新。

 

当今的自适应学习系统主要依靠对评估的响应来更新用户模型。需要新的算法将不同类型的行为转换为某种“表现”的指示。特别是，必须考虑用户在SLN中的特征，例如讨论活动的级别、评论的质量、与朋友共享私有注释的时间以及与相同学习路径上的其他人的相似性。

 

内容自适应：更新的用户画像将被映射到关于接下来显示什么内容以及如何显示决策中。可以选择不同版本的文本和视频。例如，那些在当前的指导水平上挣扎的人在前进之前会看到加固材料，而那些无聊的人则会受到先进材料的挑战。作者可以指定转换逻辑，一个学习的“平行宇宙”树就会分支出来。此外，不同学习路径的学生仍然可以通过SLN进行互动，从他们所看到的不同角度讨论材料。

 

# 研究方法

## A 数据收集

为了收集关于SLN的数据，研究团队有两种选择，每种选择都有优点和缺点:

1）使用已存在的数据：在过去的几年中，已经有相当数量的开放在线课程。其中一些课程甚至在课程结束后仍保持开放，这使得人们可以访问每个课程的论坛和其他基本信息。此外，所有来自问答门户网站的SLN数据都可以通过各自的网站访问。

**MOOC讨论区：**数据爬取。例如，我们参考了2013年夏季对MOOC[2]的大规模统计分析，我们集中研究了Coursera在7月中旬提供的所有80门课程，这些课程都在给定日期之前结束。作为数据收集的一部分，我们使用Python和Selenium库从Coursera的服务器抓取论坛内容，然后使用Beautifulsoup 将HTML解析为文本文件。总的来说，我们的数据集包括大约830K篇文章和115K个不同的用户。我们将在第三- b节中进一步讨论我们随后的分析。

 

2）生成新数据：使用现有数据也有缺点。首先，没有机会激发SLN形成的状态以进行后续的数据分析。其次，只有开放课程的数据可用。第三，或许也是最重要的一点，数据只能在一定的度量粒度范围内公开访问。除了课程讲师和平台提供商之外，很少有人能够测量视频摄制行为、学生表现以及其他对学习SLN有价值的输入形式。

第二种选择是教育工作者和数据科学家之间的合作。或者，一个团队可以投入资源创建一个全新的在线教育平台，为许多教师提供课程。

 

**3ND：** 例如，我们在2013年创建了一个非盈利的免费在线教育平台，名为“3夜完成”(3ND)。它使用一个周末(每晚1小时)作为学习的时间单位。第3项内容是定制的，从15个“3个晚上”开始，每个“3个晚上”都是由某个领域的杰出领袖在不同的主题上为这个“教育慈善机构”捐赠自己的时间。视频讲座还配有多项选择题、讨论区和视频旁边的笔记/分享框。关于用户与这些学习模式的交互的数据是在细粒度上捕获的。

视频讲座还配有多项选择题、讨论区和视频旁边的笔记/分享框。

关于用户与这些学习模式的交互的数据是在细粒度上捕获的。

 

## B 分析

一旦收集了SLN数据，下一步就是执行分析。有很多方法可以解决这个问题，这取决于研究的问题;大多数将涉及大规模的机器学习方法。为了举例说明，我们从最近的工作中总结了两个案例研究。

 

**MOOC****讨论区分析**：再次参考我们的MOOe研究[2]，在爬取数据之后我们转向分析论坛特征。通过检查，我们很快发现Coursera的辍学率高，信息过载现象普遍。因此，我们将后续的分析分为两个主要部分。

 

首先，我们使用线性回归模型来确定哪些课程属性与学生在论坛中的参与度相关，我们将这些属性量化为数据集中每门课程每天出现的帖子(或用户)数量。我们的一些初步发现包括以下内容。首先，教师积极参与论坛与讨论量的增加有关，但也与长期下降率的增加有关。类似地，当一门课程存在互评时，在短期内讨论的数量往往会增加，但在长期内下降的速度会加快。在未来，我们计划通过将SLN图的聚类系数和同质性等属性合并到模型中来重新研究这个分析。

 

其次，我们提取了每门课讨论中出现的闲聊的数量，包括(1)总体上和(2)随着时间的推移。在这两种情况下，我们都依赖于Amazon Mechanical Turk (AMT)标签。对于(1)，我们从每门课程中随机抽取30个线程，每个线程都被AMT标记为闲聊或不闲聊;我们发现大部分的课程论坛都超过了10%。对于(2)，我们从AMT数据中跨给定类别的所有课程训练了一个支持向量机分类器，并使用它在前35天对线程进行分类。我们发现，在最初的几天里，每个类别的人闲聊的次数都超过了40%。基于论坛结构的SLN信息可能会提高仅使用文本分类器的准确性。

 

**表现预测：**我们还对MOOC数据应用了一些标准的预测算法。从程序上来说，对于两门课程，我们提取所有可用的用户-测试对，并将它们收集到一个矩阵中，其中一项取三个值中的一个:如果学生回答错误，则为0，如果正确，则为1，如果他/她没有回答问题，则为NA。对N:FMB，共产生2229名学生和90个小测验，其中8.9%的条目被填满;对于NI，我们有3196名学生和69个小测试，其中23%的学生完成了测试。然后，利用70%的可用数据，我们训练了两种算法:(1)基线预测，解决最小二乘优化，使学生和测验偏差的误差最小化，(2)邻里预测，它扩展了基线，来求学生和测验的相似性。其余30%的数据用于测试训练算法的性能。

 

为了获得性能，在每种情况下，我们都在将预测四舍五入到0或1之后计算准确率。这与简单地预测每个数据集分区中所有元素平均值的朴素平均值进行了比较。我们在N:FMB中获得了接近75%的准确率，比平均值高9.2%，NI接近80%，比平均值高3.6%。这些结果很有希望，但是它们只考虑性能数据本身的结构。同样，将SLN数据合并到这些模型中可以提高这里所得到的结果的准确性。

 

## C 设计与尝试

 

除了SLN数据分析之外，还有新的学习系统的设计。随着时间的推移，后续的数据收集和分析可以帮助逐步改进这些功能。

**MIlC设计：**通过与软件工程师和用户界面设计师团队的共同努力，我们一直在开发一个软件系统，并将其作为个性化的测试平台。我们称其为移动集成个性化课程(MIIC)，因为它包含了集成和个性化的互补功能，是作为移动应用程序交付的。关于课程个性化的猜测有很多，需要通过实证研究进行检验，其结果可以推动技术的修正。最近，我们以iOS移动应用的形式，使用MIIC的原型进行了两次试验，招募了在MOOC上不同技术背景的参与者。

 

**尝试一：调查响应**。本试验中MIIC的资料是基于谷歌PageRank的两种不同解释。一个来自N:FMB和相关的文本*网络生活**:20**个问题和答案*，这需要线性代数的背景。另一个来自NI和Networks的例子:*8条不需要微积分的原则*，只需要简单的加法和乘法。图4显示了用户可以向下定向的两个不同的并行宇宙。

 ![n  Example  The calculation   • in. scive z:  (b) Basic algebra  (2)  (a) Linear algebra ](/../assets/clipimg/clip_image001-1560925923415.png)



我们将实验参与者随机分为两组。一个被要求使用MIIC，然后使用OSFA，而另一个被要求使用相反的方法。每个学生都被要求在完成这些任务后填写一份实验后调查问卷，其中43人完成了调查。在实验后的调查中，有四个问题处理了两种版本之间的直接比较。对于这些问题，我们使用[17]中描述的三叉检验来确定阳性(有利于MIIC)和阴性(有利于OSFA)应答_之间是否存在统计学上显著的差异。询问哪个版本包含了太多的难理解的材料，哪个版本更容易理解，以及哪个版本总体上更受欢迎，每个版本的问题都对MIIC有利，双边p值分别为0.025、0.008和小于0.001。

 

**尝试二：保留率。**这次试验的设置与第一次类似，除了三个重要的细节。首先，使用的材料是关于细胞功率控制的讲座，而不是谷歌PageRank。第二，每个参与者只被给予MIIC或OSFA中的一种，他们不知道他们收到的是哪一种。第三，这里的目标是衡量学生的参与度，而不是依赖调查结果。作为参与度的代理，我们选择总页数作为统计分析的端点之一。这是每个用户访问的唯一页面的总数。我们期望那些使用MIIC的人会比那些使用OSFA的人访问更多的资料，因为MIIC能够满足每个用户的个性化需求。

 

在我们的实验中，有44名学生参与了足够长的时间来进行分析。图5给出了每个组的页面总数的箱线图;MIIC的分布明显向右倾斜，表明参与度较高。然后我们使用统计测试来分析分布之间的差异。在检测到偏离正态性的情况下，我们使用非参数Wilcoxon秩和检验，其p值非常显著，为0.009。此外，我们计算了霍奇森-莱曼估计的5页，以及95%置信区间估计的1到8页，每个都有利于MIIC。这验证了使用MIIC的学生比使用OSFA的学生更倾向于学习更多的信息，这意味着适应性对参与度有积极的影响。

 

# 结论

最近在线教育的创新引起了人们对SLN作为一个研究领域的兴趣。在本文中，我们对SLN进行了简要的概述，包括它的应用、出现的研究问题和研究方法，并概述了我们在数据收集、分析和系统设计方面最近完成的几个步骤。

