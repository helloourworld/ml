---
layout: post
title: Spark-ML-0304-Ensembles
category: MLAdvance
catalog: yes
description: Ensembles 集成学习
tags:
    - Machine Learning
    - Python
---
# 集成学习

&emsp;&emsp;集成学习通过构建并结合多个学习器来完成学习任务，有时也被称为多分类器系统。集成学习通过将多个学习器进行结合，常可获得比单一学习器显著优越的泛化能力。

&emsp;&emsp;根据个体学习器的生成方式，目前的集成学习方法大致可以分为两大类。即个体学习器之间存在强依赖性，必须串行生成的序列化方法以及个体学习器之间不存在强依赖性，可同时生成的并行化方法。
前者的代表是`Boosting`，后者的代表是`Bagging`和随机森林。后面的随机森林章节会详细介绍`Bagging`和随机森林`Random Forests`；梯度提升树章节会详细介绍`Boosting`和梯度提升树`Gradient-Boosted Trees `。
