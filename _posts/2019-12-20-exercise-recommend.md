---
layout:  post
title:  Exploring Multi-Objective Exercise Recommendations in Online Education Systems
date:  2019-12-20 14:20:00
tags: [论文阅读,教育大数据]
typora-root-url: .
typora-copy-images-to: ..\assets\clipimg
---

总结：基于目标均衡的习题推荐。主要创新点：推荐习题时，综合考虑，习题难度的平滑、复习和预习、及学习者的参与程度。主要方法：强化学习与马尔科夫决策过程

 

Cognitive diagnosis：aiming at discovering student mastery levels on knowledge concepts

认知诊断：旨在发现学习者对知识概念的掌握程度

 

DINA：对每个学生的知识状态用一个向量表示，其掌握还是未掌握每个知识点

 

**问题建模**：根据学习者u的历史练习记录e，e用词向量、知识概念、和难度系数表示，从而向学习者推荐练习题

 

**解决思路**：马尔科夫决策过程

状态：学习者当前的的知识状态

行动：推荐习题et+1给学习者

奖赏：学生做题后的反馈

转移矩阵：在状态st时采取行为at后进入到的下一个状态st+1

![mdp](/../assets/clipimg/clip_image001-1576851502981.png)

 

 

 

 

 

 