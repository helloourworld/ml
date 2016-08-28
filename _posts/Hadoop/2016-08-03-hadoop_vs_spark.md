---
layout: post
title: Hadoop or Spark
category: Hadoop
catalog: yes
description:  Which Is The Best Big Data Framework? - Forbes.
tags:
    - Big Data
    - 大数据
---
THis page is mainly from [Forbes](http://www.forbes.com/sites/bernardmarr/2015/06/22/spark-or-hadoop-which-is-the-best-big-data-framework/#72907489532c) by Bernard Marr, CONTRIBUTOR

## Introduction

Hadoop and Spark are both Big Data frameworks–they provide some of the most popular tools used to carry out common Big Data-related tasks.

Hadoop, for many years, was the leading open source Big Data framework but recently the newer and more advanced Spark has become the more popular of the two Apache Software Foundation tools.

However they do not perform exactly the same tasks, and they are not mutually exclusive, as they are able to work together. Although Spark is reported to work up to 100 times faster than Hadoop in certain circumstances, it does not provide its own distributed storage system.


Distributed storage is fundamental to many of today’s Big Data projects as it allows vast multi-petabyte datasets to be stored across an almost infinite number of everyday computer hard drives, rather than involving hugely costly custom machinery which would hold it all on one device. These systems are scalable, meaning that more drives can be added to the network as the dataset grows in size.

As I mentioned, Spark does not include its own system for organizing files in a distributed way (the file system) so it requires one provided by a third-party. For this reason many Big Data projects involve installing Spark on top of Hadoop, where Spark’s advanced analytics applications can make use of data stored using the Hadoop Distributed File System (HDFS).

What really gives Spark the edge over Hadoop is speed. Spark handles most of its operations “in memory” – copying them from the distributed physical storage into far faster logical RAM memory. This reduces the amount of time consuming writing and reading to and from slow, clunky mechanical hard drives that needs to be done under Hadoop’s MapReduce system.
