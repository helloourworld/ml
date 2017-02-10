---
layout: post
title: Zipline量化平台
category: Quant
catalog: yes
description: 方案备选一
tags:
    - quant
    - Python
---
## 什么是量化策略？

*一个完整的策略需要包含输入、策略处理逻辑、输出；策略处理逻辑需要考虑选股、择时、仓位管理和止盈止损等因素。

![](/images/quant/what_is_algo.jpg)

<--https://uqer.io/community/share/567ba9c3228e5b344568822c-->

<--https://uqer.io/community/share/56749fca228e5bab38c977d1-->

## [量化策略基础架构](https://zhuanlan.zhihu.com/p/23488593)

![](/images/quant/quant_plat.png)

## 国内量化平台概览

目前市面上比较活跃的Python量化平台主要有：[优矿](https://uqer.io/contest/home/)，[米筐](https://www.ricequant.com/)，[聚宽](https://www.joinquant.com/)。其中米筐和聚宽跟国外quantopian非常相似。

1 优矿-通联量化实验室：

支持语言：Python

* **Pros**
* 1. 数据种类丰富(免费试用收费数据)
* 2. 有活跃的社区
* 3. 回测速度较快。

* **Cons**
* 1. history拿历史数据的api过多。
* 2. 界面体验不够友好。

2 米筐：

支持语言：Python Java

* **Pros**
* 1. 米筐开发实力强劲。
* 2. 平台同时支持Python和Java两种语言的回测。
* 3. 米筐的视觉设计和文档规范。

* **Cons**
* 1. 推出功能速度有些慢。
* 2. 后台稳定性有待加强。

3 聚宽：

支持语言：Python

* **Pros**
* 1. 开发速度很快，比如撤单，非复权成交等等功能，新功能上线很迅速；
* 2. 社区活跃，很多不错的ETF策略

* **Cons**
* 1. 微信推送调仓很好用。
* 2. 回测速度慢，有时候会卡死。

其它：

4 [掘金](http://www.myquant.cn/)

支持语言：Python Matalab R Java C C++ C#

5 [京东金融](https://quant.jd.com/)

支持语言：Python Java

## [Zipline](http://www.zipline.io/index.html)开发回测方案

使用Zipline（[Quantopian](https://www.quantopian.com/home)开源）搭建量化策略研究平台，解决策略回测和评价的大部分问题。

Zipline is an open-source algorithmic trading simulator written in Python.

**Features**

* Ease of use: Zipline tries to get out of your way so that you can focus on algorithm development. See below for a code example.
* Zipline comes “batteries included” as many common statistics like moving average and linear regression can be readily accessed from within a user-written algorithm.
* Input of historical data and output of performance statistics are based on Pandas DataFrames to integrate nicely into the existing PyData eco-system.
* Statistic and machine learning libraries like matplotlib, scipy, statsmodels, and sklearn support development, analysis, and visualization of state-of-the-art trading systems.

## 参考

[深入了解zipline](http://zipline.zhikuang.org/234736)
