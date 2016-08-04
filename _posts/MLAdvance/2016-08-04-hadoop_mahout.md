---
layout: post
title: Hadoop Mahout
category: MLAdvance
description:  Hadoop Family.Mahout.
---

## 开始 Mahout

Mahout has prepared a bunch of examples and tutorials for users to quickly learn how to use its machine learning algorithms.

![](/images/mahout-logo-brudman.png)

## Mahout 支持的算法

|	**Mahout 0.12.0 Features by Engine¶**	|
|		|	**Single Machine**	|	**MapReduce**	|	**Spark**	|	**H2O**	|	**Flink**	|
|	**Mahout Math-Scala Core Library and Scala DSL**	|		|		|		|		|		|
|	Mahout Distributed BLAS. Distributed Row Matrix API with R and Matlab like operators. Distributed ALS, SPCA, SSVD, thin-QR. Similarity Analysis.	|		|		|	x	|	x	|	x	|
|		|		|		|		|		|		|
|	**Mahout Interactive Shell**	|
|	Interactive REPL shell for Spark optimized Mahout DSL	|		|		|	x	|		|		|
|		|		|		|		|		|		|
|	**Collaborative Filtering with CLI drivers**	|		|		|		|		|		|
|	User-Based Collaborative Filtering	|	弃用	|	弃用	|	x	|		|		|
|	Item-Based Collaborative Filtering	|	x	|	x	|	x	|		|		|
|	Matrix Factorization with ALS	|	x	|	x	|		|		|		|
|	Matrix Factorization with ALS on Implicit Feedback	|	x	|	x	|		|		|		|
|	Weighted Matrix Factorization, SVD++	|	x	|		|		|		|		|
|		|		|		|		|		|		|
|	**Classification with CLI drivers**	|		|		|		|		|		|
|	Logistic Regression - trained via SGD	|	弃用	|		|		|		|		|
|	Naive Bayes / Complementary Naive Bayes	|		|	弃用	|	x	|		|		|
|	Hidden Markov Models	|	弃用	|		|		|		|		|
|		|		|		|		|		|		|
|	**Clustering with CLI drivers**	|		|		|		|		|		|
|	Canopy Clustering	|	弃用	|	弃用	|		|		|		|
|	k-Means Clustering	|	弃用	|	弃用	|		|		|		|
|	Fuzzy k-Means	|	弃用	|	弃用	|		|		|		|
|	Streaming k-Means	|	弃用	|	弃用	|		|		|		|
|	Spectral Clustering	|		|	弃用	|		|		|		|
|		|		|		|		|		|		|
|	**Dimensionality Reduction** note: most scala-based dimensionality reduction algorithms are available through the Mahout Math-Scala Core Library for all engines	|		|		|		|		|		|
|	Singular Value Decomposition	|	弃用	|	弃用	|	x	|	x	|	x	|
|	Lanczos Algorithm	|	弃用	|	弃用	|		|		|		|
|	Stochastic SVD	|	弃用	|	弃用	|	x	|	x	|	x	|
|	PCA (via Stochastic SVD)	|	弃用	|	弃用	|	x	|	x	|	x	|
|	QR Decomposition	|	弃用	|	弃用	|	x	|	x	|	x	|
|		|		|		|		|		|		|
|	**Topic Models**	|		|		|		|		|		|
|	Latent Dirichlet Allocation	|	弃用	|	弃用	|		|		|		|
|		|		|		|		|		|		|
|	**Miscellaneous**	|		|		|		|		|		|
|	RowSimilarityJob	|		|	弃用	|	x	|		|		|
|	Collocations	|		|	弃用	|		|		|		|
|	Sparse TF-IDF Vectors from Text	|		|	弃用	|		|		|		|
|	XML Parsing	|		|	弃用	|		|		|		|
|	Email Archive Parsing	|		|	弃用	|		|		|		|
|	Evolutionary Processes	|	x	|		|		|		|		|

[mahout algorithms]("http://mahout.apache.org/users/basics/algorithms.html")

## 说明