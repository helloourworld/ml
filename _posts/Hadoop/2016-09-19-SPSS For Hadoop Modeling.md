---
layout: post
title: SPSS for BigData Modeling
category: Hadoop
catalog: yes
description: SPSS对接Hadoop分析模式。
tags:
    - Big Data
---

# SPSS for BigData Modeling

## 1 Featured IBM Analytics products


| SPSS\|Predictive analytics     | IBM SPSS predictive analytics software offers advanced techniques in an easy-to-use package to help you find new opportunities, improve efficiency and minimize risk.                                                                            |
| IBM DB2\|Database                 | IBM DB2 is the database of choice for enterprise-wide solutions. Optimized to deliver industry-leading performance while lowering costs, IBM DB2 offers extreme performance, flexibility, scalability and reliability for any size organization. |
| Cognos Analytics on Cloud\|Business Intelligence    | Get the self-service you expect, data governance you require, and reporting you trust with a secure business intelligence software-as-a-service (SaaS) solution.                                                                                 |
| Hadoop\|Data management platform | IBM® BigInsights™ for Apache™ Hadoop is an industry standard Hadoop offering that combines the best of open source software with enterprise-grade capabilities.                                                                                  |

## 2 SPSS Analytic for BigData

### 2.1 分析服务器3.0

结合使用 IBM SPSS Modeler 与 IBM SPSS Analytic Server 时，分析人员可以在大数据上开发并部署预测性分析，而无需昂贵的技术技能。IBM SPSS Analytic Server 可提供：

* 使用大数据系统的以数据为中心的开放式集成架构
* 支持各种流行的 Hadoop 版本，例如，IBM InfoSphere® BigInsights™、Cloudera、Hortonworks 和 Apache Hadoop
* 已定义的接口，以合并旨在用于数据的新统计算法
* 熟悉的 IBM SPSS 用户界面，隐藏大数据环境的详细信息，以便分析人员可以专注于数据分析
* 可通过扩展来解决几乎任何规模问题的解决方案

**SPSS Analytic Server Architecture**

[![](E:/machinelearningadvance/images/spss_as_architecture.jpg)](http://www.ibm.com/support/knowledgecenter/SSWLVY_3.0.0/analytic_server_overview_ddita/analytic_server/overview/ae_architecture.html)

#### 2.1.1 [Analytic Server 管控台](http://www.ibm.com/support/knowledgecenter/zh/SSWLVY_3.0.0/analytic_server_user-guide_ddita/analytic_server/idh_admin_console.html)

Analytic Server provides a thin client interface for managing data sources and projects.

A data source is a collection of records, plus a data model, that define a data set for analysis. The source of records can be a file (delimited text, fixed width text, Excel) on HDFS, a relational database, HCatalog, or geospatial.

Projects are workspaces for storing inputs and accessing outputs of jobs. They provide the top-level organizational structure for containing files and folders. Projects can be shared with individual users and groups.

#### 2.1.2 [SPSS Modeler 集成](http://www.ibm.com/support/knowledgecenter/zh/SSWLVY_3.0.0/analytic_server_user-guide_ddita/analytic_server/ae_modeler_integration_overview.html)

SPSS® Modeler is a data mining workbench that has a visual approach to analysis. Each distinct action in a job, from accessing a data source to merging records to writing out a new file or building a model, is represented by a node on the canvas. We link these actions together to form an analytic stream. To build an analytic stream that runs with Analytic Server

**受支持的节点**

A stream that begins with anything other than an Analytic Server source node will be run locally.

* **Graphs**
* All Graph nodes are supported.
* **Modeling**
* The following Modeling nodes are supported: Time Series, TCM, Tree-AS, C&R Tree, Quest, CHAID, Linear, Linear-AS, Neural Net, GLE, LSVM, TwoStep-AS, Random Trees, STP, and Association Rules. Further notes on those nodes' functionality follow.

**案例**

[基于 Hadoop 平台下 Modeler 的大数据应用剖析](http://www.ibm.com/developerworks/cn/data/library/bd-1312hadoop-modeler/)

### 2.2 [Spark 和 分析服务器](http://www.ibm.com/support/knowledgecenter/zh/SSWLVY_3.0.0/analytic_server_overview_ddita/analytic_server/overview/as_spark.html)

分析服务器 与 Apache Spark 集成以提高性能。

**何时使用和不使用 Spark**

如果在 Hadoop 集群中将 Spark 作为 Ambari 服务进行安装，那么 分析服务器 使用它来处理大数据作业。 以下准则适用于确定何时不使用 Spark。

* 1 如果数据集小于 128 MB，那么 分析服务器 在 分析服务器 JVM 中使用嵌入式 MapReduce 函数并且不使用 Spark 或 Hadoop 集群。
* 2 如果未在集群上安装 Spark，那么 分析服务器 使用 MapReduce v2。
* 3 分析服务器 使用 MapReduce v2 来构建 PSM 模型。 作业以 PSM 模型构建结束时，分析服务器 使用 Spark 通过导致模型构建的所有步骤来处理作业，并写入磁盘，然后使用 MapReduce 来构建 PSM 模型。 例如，如果作业包含后面跟着 PSM 模型构建的连接，那么在 Spark 中运行连接并在 MapReduce 中的连接数据上运行 PSM。

**案例**

[SPSS Algorithms Optimized for Apache Spark & Spark Algorithms Extending SPSS Modeler](https://developer.ibm.com/predictiveanalytics/2015/11/06/spss-algorithms-optimized-for-apache-spark-spark-algorithms-extending-spss-modeler/)

![](E:/machinelearningadvance/images/spss_spark_table1105.png)

### 2.3 [使用 SPSS + BigInsights 共同构架大数据分析平台](http://www.tuicool.com/articles/V3QVn23)

## 3 Supported Language with [SPSS Modeler](http://www.ibm.com/support/knowledgecenter/SS3RA7) 18.0

[ModelerUsersGuide.pdf](ftp://public.dhe.ibm.com/software/analytics/spss/documentation/modeler/18.0/en/ModelerUsersGuide.pdf)

### 3.1 R

IBM® SPSS® Modeler supports R.

f you have a compatible copy of R installed, you can connect to it from IBM SPSS Modeler and carry out model building and model scoring using custom R algorithms that can be deployed in IBM SPSS Modeler. You must also have a copy of IBM SPSS Modeler - Essentials for R installed. IBM SPSS Modeler - Essentials for R provides you with tools you need to start developing custom R applications for use with IBM SPSS Modeler.

[参考链接](http://www.ibm.com/support/knowledgecenter/SS3RA7_18.0.0/modeler_r_nodes_ddita/clementine/r_nodes.html)

### 3.2 Python for Spark

IBM® SPSS® Modeler supports Python scripts for Apache Spark.

[参考链接](http://www.ibm.com/support/knowledgecenter/SS3RA7_18.0.0/modeler_r_nodes_ddita/clementine/r_intro_pyspark.html)

**案例**

[Coding a Python/Spark Modeler Extension for Collaborative Filtering](https://developer.ibm.com/predictiveanalytics/2016/03/07/coding-a-pythonspark-modeler-extension-for-collaborative-filtering/)


## 4 Other resource

[IBM Analytics](http://www.ibm.com/analytics/us/en/)

[Stream Computing](http://www.ibm.com/analytics/us/en/technology/stream-computing/)

[IBM Cloud Computing](http://www.ibm.com/cloud-computing/)

[Comparing the leading big data analytics software options](http://searchbusinessanalytics.techtarget.com/feature/Comparing-the-leading-big-data-analytics-software-options)

[Social Nodes for SPSS Modeler](https://developer.ibm.com/predictiveanalytics/2016/09/07/social-nodes-for-spss-modeler/)

[IBM Watson Analytics](https://www.ibm.com/analytics/watson-analytics/us-en/)
