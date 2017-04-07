---
layout: post
title: Spark-ML-05-Clustering
category: MLAdvance
catalog: yes
description: Clustering is an unsupervised learning problem whereby we aim to group subsets of entities with one another based on some notion of similarity. Clustering is often used for exploratory analysis and/or as a component of a hierarchical supervised learning pipeline (in which distinct classifiers or regression models are trained for each cluster).
tags:
    - Machine Learning
    - Spark
    - Clustering
---
### 聚类

&emsp;&emsp;[聚类](https://en.wikipedia.org/wiki/Cluster_analysis)是一种无监督学习问题，它的目标就是基于相似度将相似的子集聚合在一起。聚类经常用于探索性研究或者作为分层有监督流程的一部分。
`spark.mllib`包中支持下面的模型。

 * [k-means算法](/mladvance/2017/04/07/clustering/)
 * [GMM（高斯混合模型）](/mladvance/2017/04/07/gaussian-mixture/)
 * [PIC（快速迭代聚类）](PIC/pic.md)
 * [LDA（隐式狄利克雷分布)](LDA/lda.md)
 * [二分k-means算法](bis-k-means/bisecting-k-means.md)
 * [流式k-means算法](streaming-k-means/streaming-k-means.md)
