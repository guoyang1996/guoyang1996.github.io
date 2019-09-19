---
layout:  post
title:  研究知识追踪/学生模型的一些学校和人物
date:  2019-09-18 14:20:00
tags: [教育大数据]
typora-root-url: .
typora-copy-images-to: ..\assets\clipimg
---

转载自https://blog.csdn.net/hohomi77/article/details/81331006

### 宾夕法尼亚大学

Dr. Ryan Baker
Edx上《Big Data and Education》课程的老师。
CMU毕业。他的两位导师：Kenneth R. Koedinger是PFA论文第三作者，Albert Corbett是KBT的第一作者。

### 伍斯特理工学院(Worcester Polytechnic Institute)

Neil T. Heffernan教授
是常见数据集ASSISTment的作者。
他团队的几个中国研究生：一、Liang Zhang硕士已经毕业，硕士论文是《Incorporating Rich Features into Deep Knowledge Tracing》
二、Xiaolu Xiong博士生。发表不少论文。其中《Going Deeper with Deep Knowledge Tracing》，在Baker的教育大数据Edx课上有提到。
他2017年3月份博士毕业了。博士论文《Theory and Practice: Improving Retention Performance through Student Modeling and System Building》。
2018年3月进入Facebook做软件工程师（机器学习）。

三、更早的Yue Gong博士。2010年文章《How to Construct More Accurate Student Models: Comparing and Optimizing Knowledge Tracing and Performance Factor Analysis》。她已经博士毕业目前在Facebook工作。
她的博士论文《Student Modeling in Intelligent Tutoring Systems》2014年。

四、Yutao Wang(南开大学毕业，2015年WPI博士毕业），现在在Google做软件工程师。
2013年的文章。把贝叶斯知识追踪拓展到实数域（不再是非对即错）。

五、Mingyu Feng（2009年博士毕业）现在在SRI国际做研究员。
她和乂学教育教育有合作。2018年CSEDU上的一篇短论文。20分钟报告。题目《Yixue Adaptive Learning System and Its Promise on Improving Student Learning》

六、赵思源。《Going Deeper》一文的合作者。2018年毕业，去亚马逊做应用科学家。

### 伯克利

Zachary A. Pardos，他是Heffernan的学生。
他扩展了BKT模型，还编写了工具xBKT：https://github.com/CAHLR/xBKT
他的硕士生Cristian Garay（2017年起在微软做软件工程师）移植到python上，叫pyBKT。
他积极参与MOOC的研究。我之前看过的《Communication at Scale in a MOOC Using Predictive Engagement Analytics》就是他的文章！

### 卡内基梅隆大学

Albert Corbett和John Robert Anderson（70岁），提出了最早的知识追踪模型《贝叶斯知识追踪》。后者是Neil Heffernan的老师。
Michael Yudelson(博士后、访问学者)现在已经进了公司。

另外不属于这个主题的。Ken Koedinger，也就是Ryan Baker的导师。最近有一篇《Data-Driven Hint Generation in Vast Solution Spaces: a Self-Improving Python Programming Tutor》，智能产生python解题提示的。

### 香港科技大学

Dit-Yan YEUNG 楊瓞仁（代理系主任）
研究机器学习，题目非常广泛，并非专注教育。
他的硕士生Chun-Kit Yeung用改进的DKT赢得了2017年的ASSISTments Longitudinal Data Mining Competition比赛！
他的博士生Xingjian Shi(施行健)与下面这位张佳妮有两篇一二作者合作。

### 香港中文大学

Irwin King
团队的张佳妮(2015年毕业于上海交通大学)在WWW 2017提出《Dynamic key-value memory networks for knowledge tracing》 似乎是另外一种基于神经网络的知识追踪。

### 孟菲斯大学

Philip I. Pavlik Jr
他提出了贝叶斯知识追踪的主要对手“Performance Factor Analysis”，他本人是心理学系的教授。并不专注于计算机领域。

### 加拿大蒙特利尔大学

Desmarais, M.C., Meshkinfam, P., Gagnon, M. (2006) Learned Student Models with Item to Item Knowledge Structures. User Modeling and User-Adapted Interaction, 16, 5, 403-434.
这位Michel Desmarais是华侨大学傅顺开（兜兜公交、帮帮行、淘流量创始人）的导师。最近偶尔在搞Q-Matrix分解。

### 科罗拉多大学博尔德分校

Michael C. Mozer教授。他们有一篇《How Deep is Knowledge Tracing》作者Mohammad M. Khajah，是质疑DKT的文章。把BKT的缺点和改进办法讲得很详细，是会议的最佳论文。

### 匹兹堡大学

Peter Brusilovsky的博士生Yun Huang（毕业于北邮、计算所）。

### 北卡罗来纳州立大学

Tiffany Barnes教授。Q-matrix方法的提出者。
她团队有中国留学博士生Ye Mao小姐姐。她的github上有很多知识推断算法的实现，应该是课程作业。(https://github.com/yemao616)

### 斯坦福大学

Chris Piech助理教授
2015年提出DKT。DKT是目前预测准确度最高的。不过这位大哥竟然把训练数据用于测试。

补充内容(2018-8-4)

### 伯克利

两个硕士生（Plan II的，就是不用毕业论文，只要项目技术报告）
Daniel Armendariz（2014年毕业）作品OCTAL，涉及concept graph、knowledge estimate、edx
（他现在在荷兰Amazon开发Cloud9 IDE）
Zhiping Xiao（2018年毕业）作品AutoQuiz，可以在github下载到(https://github.com/PatriciaXiao/AutoQuiz_v2)

### 沪江英语

王新义 - 自适应学习：机器学习在开心词场中应用
提到IRT，记忆模型ACT-R、MCM、Duolingo等等。（看来是来自Ryan Baker的课程）
参见：https://juejin.im/post/5aaf7d48f265da239706ab0f

义学教育
Dan Bindman？？

------

版权声明：本文为CSDN博主「hohomi77」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/hohomi77/article/details/81331006