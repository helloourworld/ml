---
layout: post
title: Hadoop Resource
category: MLAdvance
description: Limited site of Hadoop Resource.
---
## Apache

[Apache docs](http://hadoop.apache.org/docs/r2.6.4/hadoop-project-dist)


## Cloudera

[Cloudera Engineering Blog](http://blog.cloudera.com/)


## hortonworks

[hortonworks公司中文网](http://zh.hortonworks.com/)

[THE HDPCD EXAM](http://zh.hortonworks.com/training/certification/hdpcd-certification/)

[Hortonworks(HDP)开发者认证](http://ifeve.com/hortonworkshdp-hdpcd/)

### 考试内容列表

|   类型  |   [任务]    |
|   数据获取    |   [通过Hadoop Shell把本地文件上传到HDFS](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#put)  |
|       |   [使用Hadoop Shell在HDFS上创建一个新的目录](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#mkdir)  |
|       |   [从一个关系型数据库中导入数据到HDFS](http://sqoop.apache.org/docs/1.4.5/SqoopUserGuide.html#_literal_sqoop_import_literal) |
|       |   [导入关系型数据的查询结果到HDFS](http://sqoop.apache.org/docs/1.4.5/SqoopUserGuide.html#_free_form_query_imports)    |
|       |   [从一个关系型数据库中导入数据到一个新的或者已经存在的Hive表里](http://sqoop.apache.org/docs/1.4.5/SqoopUserGuide.html#_importing_data_into_hive)    |
|       |   [从 HDFS里面插入和更新数据到关系型数据库里面](http://sqoop.apache.org/docs/1.4.5/SqoopUserGuide.html#_literal_sqoop_export_literal)    |
|       |   [ 给你一个Flume配置文件，启动一个 Flume agent](https://flume.apache.org/FlumeUserGuide.html#starting-an-agent)   |
|       |   [[给你一个配置好的 sink 和source, 配置一个 Flume 固定容量的内存 channel](https://flume.apache.org/FlumeUserGuide.html#memory-channel)](https://flume.apache.org/FlumeUserGuide.html#memory-channel) |
|   数据转换    |   [写出并执行一个pig脚本](https://pig.apache.org/docs/r0.14.0/start.html#run)  |
|       |   [ 加载一个没有schema信息数据到Pig](https://pig.apache.org/docs/r0.14.0/basic.html#load)    |
|       |   [加载数据到Pig里面并关联一个schema](https://pig.apache.org/docs/r0.14.0/basic.html#load)    |
|       |   [从Hive表里面加载数据到Pig](https://cwiki.apache.org/confluence/display/Hive/HCatalog+LoadStore) |
|       |   [通过Pig把加载的数据格式化](https://pig.apache.org/docs/r0.14.0/basic.html#foreach)    |
|       |   [转换数据匹配一个给定的Hive schema](https://pig.apache.org/docs/r0.14.0/basic.html#foreach)    |
|       |   [对 Pig 中数据进行分组](https://pig.apache.org/docs/r0.14.0/basic.html#group)   |
|       |   [使用Pig移除记录里面关联的空值](https://pig.apache.org/docs/r0.14.0/basic.html#filter)   |
|       |   [把 Pig 中的数据保存到HDFS中指定目录里面](https://pig.apache.org/docs/r0.14.0/basic.html#store)    |
|       |   [把 Pig中的数据保存到Hive表里](https://cwiki.apache.org/confluence/display/Hive/HCatalog+LoadStore)   |
|       |   [对Pig数据进行排序输出](https://pig.apache.org/docs/r0.14.0/basic.html#order-by) |
|       |   [把Pig中关联重复数据移除](https://pig.apache.org/docs/r0.14.0/basic.html#distinct)    |
|       |   [对Pig MapReduce指定reduce任务数量](https://pig.apache.org/docs/r0.14.0/perf.html#parallel)    |
|       |   [使用Pig进行关联操作](https://pig.apache.org/docs/r0.14.0/basic.html#join-innerandhttps://pig.apache.org/docs/r0.14.0/basic.html#join-outer)    |
|       |   [通过Pig join操作生成一个副本](https://pig.apache.org/docs/r0.14.0/perf.html#replicated-joins)    |
|       |   [ 运行一个Pig 任务通过 Tez](https://pig.apache.org/docs/r0.14.0/perf.html#tez-mode) |
|       |   [在一个Pig 脚本内,通过注册一个Jar来使用定义的函数](https://pig.apache.org/docs/r0.14.0/basic.html#registerandhttps://pig.apache.org/docs/r0.14.0/udf.html#piggybank)    |
|       |   [在Pig 脚本内, 使用定义的函数定义一个别名](https://pig.apache.org/docs/r0.14.0/basic.html#define-udfs)   |
|       |   [在一个Pig 脚本内, 执行一个用户定义函数](https://pig.apache.org/docs/r0.14.0/basic.html#register)   |
|   数据分析    |   [写并执行一个HIve查询](https://cwiki.apache.org/confluence/display/Hive/Tutorial)   |
|       |   [定义一个内部表](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-Create/Drop/TruncateTable)  |
|       |   [定义一个扩展表](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-ExternalTables) |
|       |   [定义一个分区表](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables)  |
|       |   [定义一个桶表](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-BucketedSortedTables)    |
|       |   [通过查询数据定义一个表](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-CreateTableAsSelect(CTAS))  |
|       |   [使用ORCFile 文件格式定义一个表](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/)  |
|       |   [创建一个新的 ORCFile 表从一个非-ORCFile文件的 Hive 表](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/)   |
|       |   [为Hive表指定一个存储格式](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-RowFormat,StorageFormat,andSerDe)    |
|       |   [为Hive表指定一个分隔符](http://hortonworks.com/hadoop-tutorial/using-hive-data-analysis/)   |
|       |   [加载一个目录数据到Hive表中](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Loadingfilesintotables) |
|       |   [从HDFS目录中加载数据到Hive表中](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Loadingfilesintotables) |
|       |   [把查询的结果加载数据到Hive表中](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-InsertingdataintoHiveTablesfromqueries)   |
|       |   [加载一个压缩数据到Hive表中](https://cwiki.apache.org/confluence/display/Hive/CompressedStorage)   |
|       |   [ 在Hive表中更新一行记录](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Update)  |
|       |   [从 Hive表中删除一条数据](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Delete)  |
|       |   [插入一条数据到 Hive 表中](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-InsertingvaluesintotablesfromSQL)   |
|       |   [对Hive表进行Join操作](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Joins) |
|       |   [ 通过Tez来执行Hive查询](http://hortonworks.com/hadoop-tutorial/supercharging-interactive-queries-hive-tez/)   |
|       |   [使用向量化来执行 Hive 查询](http://hortonworks.com/hadoop-tutorial/supercharging-interactive-queries-hive-tez/)  |
|       |   [输出Hive执行计划操作结果](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Explain)   |
|       |   [ 对Hive进行子查询操作](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+SubQueries) |
|       |   [输出Hive统计、排序、交叉、多重操作的查询结果](https://issues.apache.org/jira/browse/HIVE-1402) |
|       |   [设置Hadoop 或Hive 配置属性通过Hive的查询结果中](https://cwiki.apache.org/confluence/display/Hive/AdminManual+Configuration#AdminManualConfiguration-ConfiguringHive)  |


## Other

