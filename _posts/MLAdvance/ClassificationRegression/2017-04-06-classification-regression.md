---
layout: post
title: Spark-ML-03-Classification-Regression
category: MLAdvance
catalog: yes
description: 分类与回归
tags:
    - Machine Learning
    - Python
---
# 分类与回归

&emsp;&emsp;`spark.mllib`提供了多种方法用于用于[二分类](http://en.wikipedia.org/wiki/Binary_classification)、[多分类](http://en.wikipedia.org/wiki/Multiclass_classification)以及[回归分析](http://en.wikipedia.org/wiki/Regression_analysis)。
下表介绍了每种问题类型支持的算法。

| 问题类型       | 支持的方法   |
| ------------- |:-------------:|
| 二分类        | 线性SVMs、逻辑回归、决策树、随机森林、梯度增强树、朴素贝叶斯 |
| 多分类        | 逻辑回归、决策树、随机森林、朴素贝叶斯 |
| 回归          | 线性最小二乘、决策树、随机森林、梯度增强树、保序回归 |

&emsp;&emsp;点击链接，了解具体的算法实现。

* 分类和回归
    * [线性模型](/mladvance/2017/04/06/linear-model/)
        * [SVMs(支持向量机)](/mladvance/2017/04/06/lsvm/)
        * [逻辑回归](/mladvance/2017/04/06/logic-regression/)
        * [线性回归](/mladvance/2017/04/06/regression/)
    * [朴素贝叶斯](/mladvance/2016/12/05/nb/)
    * [决策树](/mladvance/2016/12/05/decision-tree/)
    * [组合树](组合树/readme.md)
        * [随机森林](/mladvance/2017/04/06/random-forests/)
        * [梯度提升树](/mladvance/2017/04/06/gbts/)
    * [保序回归](保序回归/isotonic-regression.md)
