---
layout: post
title: Hadoop Family
category: MLAdvance
description:  Hadoop Family. Hadoop 生态圈学习与体会。
tags:
    - Big Data
    - 大数据
---
Hadoop家族产品，很多。常用的项目包括Hadoop, Hive, Pig, HBase, Sqoop, Mahout, Zookeeper, Avro, Ambari, Chukwa，YARN, Hcatalog, Oozie, Cassandra, Hama, Whirr, Flume, Bigtop, Crunch, Hue, Spark, Streaming, Kafka等。对照[Hadoop_Ecosystem_Table](/Hadoop_Ecosystem_Table),我们将其分类为：

* 1	分布式文件存储系统
* 2 分布式编程
* 3 数据库[列存储、文档存储、流存储、KV存储、图数存储]
* 4 新型数据库
* 5 SQL on Hadoop
* 6 数据处理
* 7 服务系统
* 8 调度系统
* 9 机器学习
* 10 其它[标杆及问答、安全、元数据、系统部署、应用、部署架构等等]

每一分类都是方向上的概括，如同炊具，在其下面都包含多种细类，每一细类旨在解决不同的问题，如同具体的高压锅，面包机等。这一比喻请参照[Hadoop生态圈](/hadoop_family_zhihu)的描述。

## Introduction

以下图片为广泛传阅的生态图，较早为2015年，出处不详。

![](/images/Hadoop-Ecosystem-Map_2015.gif)

以下图片为最新且常用的生态图，图片源自[Safari](https://www.safaribooksonline.com/library/view/hadoop-essentials/9781784396688/ch02s05.html)
![](/images/Hadoop-Ecosystem-Map_2016.jpg)，是本文主要介绍的部分。

## 1 Hadoop Core

主要包括HDFS，MapReduce, HBase.

### 1 HDFS

Hadoop Distributed File System (HDFS™): A distributed file system that provides high-throughput access to application data. HDFS 是一个能够面向大规模数据使用的，可进行扩展的文件存储与传递系统，是一种允许文件通过网络在多台主机上分享的文件系统，可让多机器上的多用户分享文件和存储空间。实际上，通过网络来访问文件的动作，在程序与用户看来，就像是访问本地的磁盘一般。即使系统中有某些节点脱机，整体来说系统仍然可以持续运作而不会有数据损失。

#### 1 HDFS体系结构

![](/images/hdfs_architecture.jpg)

 * >1 Namenode

Namenode是整个文件系统的管理节点。它维护着整个文件系统的文件目录树，文件/目录的元信息和每个文件对应的数据块列表, 接收用户的操作请求。

文件包括：

	> ①fsimage:元数据镜像文件。存储某一时段NameNode内存元数据信息。
	> ②edits:操作日志文件。
	> ③fstime:保存最近一次checkpoint的时间

以上这些文件是保存在linux的文件系统中。通过hdfs-site.xml的dfs.namenode.name.dir属性进行设置。

查看NameNode的fsimage与edits内容

这个两个文件中的内容使用普通文本编辑器是无法直接查看的，幸运的是hadoop为此准备了专门的工具用于查看文件的内容，这些工具分别为oev和oiv，可以使用hdfs调用执行。

	`
	# 启动服务器：bin/hdfs oiv -i 某个fsimage文件

	bash$ bin/hdfs oiv -i fsimage
	14/04/07 13:25:14 INFO offlineImageViewer.WebImageViewer: WebImageViewer started.
	Listening on /127.0.0.1:5978. Press Ctrl+C to stop the viewer.

	# 查看内容：bin/hdfs dfs -ls -R webhdfs://127.0.0.1:5978/

	bash$ bin/hdfs dfs -ls webhdfs://127.0.0.1:5978/
	Found 2 items
	drwxrwx–* - root supergroup 0 2014-03-26 20:16 webhdfs://127.0.0.1:5978/tmp
	drwxr-xr-x - root supergroup 0 2014-03-31 14:08 webhdfs://127.0.0.1:5978/user
	`

	`# 导出fsimage的内容：bin/hdfs oiv -p XML -i
	tmp/dfs/name/current/fsimage_0000000000000000055 -o fsimage.xml

	bash$ bin/hdfs oiv -p XML -i fsimage -o fsimage.xml
	0000055 -o fsimage.xml

	# 查看edtis的内容：bin/hdfs oev -i
	tmp/dfs/name/current/edits_0000000000000000057-0000000000000000186 -o edits.xml

	bash$ bin/hdfs oev -i
	tmp/dfs/name/current/edits_0000000000000000057-0000000000000000186 -o edits.xml
	`

 * >2 Datanode

提供真实文件数据的存储服务。

文件块（ block）： 最基本的存储单位。

对于文件内容而言，一个文件的长度大小是size，那么从文件的０偏移开始，按照固定的大小，顺序对文件进行划分并编号，划分好的每一个块称一个Block。 HDFS默认Block大小是128MB， 因此，一个256MB文件，共有256/128=2个Block.

与普通文件系统不同的是，在 HDFS中，如果一个文件小于一个数据块的大小，并不占用整个数据块存储空间。

Replication：多复本。默认是三个。通过hdfs-site.xml的dfs.replication属性进行设置。

 * >3 数据存储： staging

 HDFS client上传数据到HDFS时，首先，在本地缓存数据，当数据达到一个block大小时，请求NameNode分配一个block。 NameNode会把block所在的DataNode的地址告诉HDFS client。 HDFS client会直接和DataNode通信，把数据写到DataNode节点一个block文件中。

 * >4 数据存储：读文件操作

![](/images/hdfs_read.jpg)
---

>> 1.首先调用FileSystem对象的open方法，其实是一个DistributedFileSystem的实例。

>> 2.DistributedFileSystem通过rpc获得文件的第一批block的locations，同一个block按照重复数会返回多个locations，这些locations按照hadoop拓扑结构排序，距离客户端近的排在前面。

>> 3.前两步会返回一个FSDataInputStream对象，该对象会被封装DFSInputStream对象，DFSInputStream可以方便的管理datanode和namenode数据流。客户端调用read方法，DFSInputStream最会找出离客户端最近的datanode并连接。

>> 4.数据从datanode源源不断的流向客户端。

>> 5.如果第一块的数据读完了，就会关闭指向第一块的datanode连接，接着读取下一块。这些操作对客户端来说是透明的，客户端的角度看来只是读一个持续不断的流。

>> 6.如果第一批block都读完了， DFSInputStream就会去namenode拿下一批block的locations，然后继续读，如果所有的块都读完，这时就会关闭掉所有的流。

>>[Alert!]如果在读数据的时候， DFSInputStream和datanode的通讯发生异常，就会尝试正在读的block的排序第二近的datanode,并且会记录哪个datanode发生错误，剩余的blocks读的时候就会直接跳过该datanode。 DFSInputStream也会检查block数据校验和，如果发现一个坏的block,就会先报告到namenode节点，然后DFSInputStream在其他的datanode上读该block的镜像。

>>该设计就是客户端直接连接datanode来检索数据并且namenode来负责为每一个block提供最优的datanode， namenode仅仅处理block location的请求，这些信息都加载在namenode的内存中，hdfs通过datanode集群可以承受大量客户端的并发访问。

 * >5 数据存储：写文件操作

 ![](/images/hdfs_write.jpg)

>>1.客户端通过调用DistributedFileSystem的create方法创建新文件。

>>2.DistributedFileSystem通过RPC调用namenode去创建一个没有blocks关联的新文件，创建前， namenode会做各种校验，比如文件是否存在，客户端有无权限去创建等。如果校验通过， namenode就会记录下新文件，否则就会抛出IO异常。

>>3.前两步结束后，会返回FSDataOutputStream的对象，与读文件的时候相似， FSDataOutputStream被封装成DFSOutputStream。DFSOutputStream可以协调namenode和datanode。客户端开始写数据到DFSOutputStream，DFSOutputStream会把数据切成一个个小的packet，然后排成队列data quene。

>>4.DataStreamer会去处理接受data quene，它先询问namenode这个新的block最适合存储的在哪几个datanode里（比如重复数是3，那么就找到3个最适合的datanode），把他们排成一个pipeline。DataStreamer把packet按队列输出到管道的第一个datanode中，第一个datanode又把packet输出到第二个datanode中，以此类推。

>>5.DFSOutputStream还有一个对列叫ack quene，也是由packet组成，等待datanode的收到响应，当pipeline中的所有datanode都表示已经收到的时候，这时akc quene才会把对应的packet包移除掉。

>>如果在写的过程中某个datanode发生错误，会采取以下几步：

>>* 1) pipeline被关闭掉；

>>* 2)为了防止防止丢包ack quene里的packet会同步到data quene里；

>>* 3)把产生错误的datanode上当前在写但未完成的block删掉；

>>* 4)block剩下的部分被写到剩下的两个正常的datanode中；

>>* 5)namenode找到另外的datanode去创建这个块的复制。当然，这些操作对客户端来说是无感知的。

>>6.客户端完成写数据后调用close方法关闭写入流。

>>7.DataStreamer把剩余得包都刷到pipeline里，然后等待ack信息，收到最后一个ack后，通知datanode把文件标视为已完成。

>>注意：客户端执行write操作后，写完的block才是可见的，正在写的block对客户端是不可见的，只有调用sync方法，客户端才确保该文件的写操作已经全部完成，当客户端调用close方法时，会默认调用sync方法。是否需要手动调用取决你根据程序需要在数据健壮性和吞吐率之间的权衡。

 * >5 HDFS常用命令

[HDFS 常用命令: 官网](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

|	类型	|	[任务]	|
|	数据获取	|	[通过Hadoop Shell把本地文件上传到HDFS](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#put)	|
|		|	[使用Hadoop Shell在HDFS上创建一个新的目录](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#mkdir)	|
|		|	[从一个关系型数据库中导入数据到HDFS](http://sqoop.apache.org/docs/1.4.5/SqoopUserGuide.html#_literal_sqoop_import_literal)	|
|		|	[导入关系型数据的查询结果到HDFS](http://sqoop.apache.org/docs/1.4.5/SqoopUserGuide.html#_free_form_query_imports)	|
|		|	[从一个关系型数据库中导入数据到一个新的或者已经存在的Hive表里](http://sqoop.apache.org/docs/1.4.5/SqoopUserGuide.html#_importing_data_into_hive)	|
|		|	[从 HDFS里面插入和更新数据到关系型数据库里面](http://sqoop.apache.org/docs/1.4.5/SqoopUserGuide.html#_literal_sqoop_export_literal)	|
|		|	[ 给你一个Flume配置文件，启动一个 Flume agent](https://flume.apache.org/FlumeUserGuide.html#starting-an-agent)	|
|		|	[给你一个配置好的 sink 和source, 配置一个 Flume 固定容量的内存 channel](https://flume.apache.org/FlumeUserGuide.html#memory-channel)	|

### 2 MapReduce

#### Explain Map/Reduce in 5 minutes

原文链接: [http://www.csdn.net/article/2013-01-07/2813477-confused-about-mapreduce](http://www.csdn.net/article/2013-01-07/2813477-confused-about-mapreduce) by Aurelien.

* >1 Map/Reduce 有3个阶段 : Map/Shuffle/Reduce

Shuffle部分由Hadoop的自动完成，我们只需要实现Map和Reduce部分。

* >2 Map部分的输入

![](/images/mapred_mapinput.jpg)

图上的城市名称作为key值，所属州以及城市均温为key的value值。ps: 请自动脑补python的dict, jason.

* > 3 Map部分的输出

![](/images/mapred_mapoutput.jpg)

图上显示的Map部分的输出是根据最终我们想要的结果来实现的。我们要得到每个州的平均值，所以根据每个州来进行新的key/value设计。

* > 4 Shuffle部分

![](/images/mapred_shuffle.jpg)

Now, the shuffle task will run on the output of the Map task. It is going to group all the values by Key, and you’ll get a List<Value>.

* > 5 Rduce部分

Reduce部分的输入为以上Shuffle部分的输出。

Reduce任务是数据逻辑的最终完成者，in our case当然就是计算各州的平均温度。最终结果如下：

![](/images/mapred_reduce.jpg)

* > 6 总结

Map/Reduce的并行执行过程：

Mapper<K1，V1> ==》 <K2，V2>

Reducer<K2，List<V2> >==》<K3，V3>

PS: You can find the java code for this example here:

[https://github.com/jsoftbiz/mapreduce_1](https://github.com/jsoftbiz/mapreduce_1)

#### 实例演示

* > 1 演示WordCount

* > 2 演示上述平均温度统计

资源链接：[https://github.com/jsoftbiz/mapreduce_1](https://github.com/jsoftbiz/mapreduce_1)

需要使用到[streaming](http://hadoop.apache.org/docs/r2.6.4/hadoop-mapreduce-client/hadoop-mapreduce-client-core/HadoopStreaming.html)

	`
[hadoop@NN01 temperature]$ hadoop jar /home/hadoop/hadoop2.6/share/hadoop/tools/lib/*streaming*.jar -input /temperature/input/* -output /temperature/py-output -file mapper.py -mapper mapper.py -file reducer.py -reducer reducer.py
16/08/09 22:26:30 WARN streaming.StreamJob: -file option is deprecated, please use generic option -files instead.
packageJobJar: [mapper.py, reducer.py, /tmp/hadoop-unjar8704173289988394156/] [] /tmp/streamjob6992547910816721479.jar tmpDir=null
16/08/09 22:26:32 INFO client.RMProxy: Connecting to ResourceManager at NN01.HadoopVM/192.168.71.128:8032
16/08/09 22:26:32 INFO client.RMProxy: Connecting to ResourceManager at NN01.HadoopVM/192.168.71.128:8032
16/08/09 22:26:33 INFO mapred.FileInputFormat: Total input paths to process : 1
16/08/09 22:26:33 INFO mapreduce.JobSubmitter: number of splits:2
16/08/09 22:26:33 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1470792183416_0002
16/08/09 22:26:34 INFO impl.YarnClientImpl: Submitted application application_1470792183416_0002
16/08/09 22:26:34 INFO mapreduce.Job: The url to track the job: http://NN01.HadoopVM:8088/proxy/application_1470792183416_0002/
16/08/09 22:26:34 INFO mapreduce.Job: Running job: job_1470792183416_0002
16/08/09 22:26:47 INFO mapreduce.Job: Job job_1470792183416_0002 running in uber mode : false
16/08/09 22:26:47 INFO mapreduce.Job:  map 0% reduce 0%
16/08/09 22:26:57 INFO mapreduce.Job:  map 50% reduce 0%
16/08/09 22:26:58 INFO mapreduce.Job:  map 100% reduce 0%
16/08/09 22:27:06 INFO mapreduce.Job:  map 100% reduce 100%
16/08/09 22:27:06 INFO mapreduce.Job: Job job_1470792183416_0002 completed successfully
16/08/09 22:27:06 INFO mapreduce.Job: Counters: 49
	File System Counters
		FILE: Number of bytes read=78
		FILE: Number of bytes written=330581
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=407
		HDFS: Number of bytes written=18
		HDFS: Number of read operations=9
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=2
	Job Counters
		Launched map tasks=2
		Launched reduce tasks=1
		Data-local map tasks=2
		Total time spent by all maps in occupied slots (ms)=16690
		Total time spent by all reduces in occupied slots (ms)=6278
		Total time spent by all map tasks (ms)=16690
		Total time spent by all reduce tasks (ms)=6278
		Total vcore-milliseconds taken by all map tasks=16690
		Total vcore-milliseconds taken by all reduce tasks=6278
		Total megabyte-milliseconds taken by all map tasks=17090560
		Total megabyte-milliseconds taken by all reduce tasks=6428672
	Map-Reduce Framework
		Map input records=9
		Map output records=9
		Map output bytes=54
		Map output materialized bytes=84
		Input split bytes=222
		Combine input records=0
		Combine output records=0
		Reduce input groups=3
		Reduce shuffle bytes=84
		Reduce input records=9
		Reduce output records=3
		Spilled Records=18
		Shuffled Maps =2
		Failed Shuffles=0
		Merged Map outputs=2
		GC time elapsed (ms)=2002
		CPU time spent (ms)=5260
		Physical memory (bytes) snapshot=685187072
		Virtual memory (bytes) snapshot=6260936704
		Total committed heap usage (bytes)=498597888
	Shuffle Errors
		BAD_ID=0
		CONNECTION=0
		IO_ERROR=0
		WRONG_LENGTH=0
		WRONG_MAP=0
		WRONG_REDUCE=0
	File Input Format Counters
		Bytes Read=185
	File Output Format Counters
		Bytes Written=18
16/08/09 22:27:06 INFO streaming.StreamJob: Output directory: /temperature/py-output
	·

* > 3 Writing an Hadoop MapReduce Program in Python

原文请参照：

[Writing an Hadoop MapReduce Program in Python](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/)

	`
[hadoop@NN01 hadoop]$ hadoop jar /home/hadoop/hadoop2.6/share/hadoop/tools/lib/*streaming*.jar -file /home/hadoop/words/mapper.py -mapper /home/hadoop/words/mapper.py -file /home/hadoop/words/reducer.py -reducer /home/hadoop/words/reducer.py -input /words/input/* -output /words/py-output
16/08/09 20:10:28 WARN streaming.StreamJob: -file option is deprecated, please use generic option -files instead.
packageJobJar: [/home/hadoop/words/mapper.py, /home/hadoop/words/reducer.py, /tmp/hadoop-unjar5100483106189698643/] [] /tmp/streamjob6336434171947928949.jar tmpDir=null
16/08/09 20:10:29 INFO client.RMProxy: Connecting to ResourceManager at NN01.HadoopVM/192.168.71.128:8032
16/08/09 20:10:29 INFO client.RMProxy: Connecting to ResourceManager at NN01.HadoopVM/192.168.71.128:8032
16/08/09 20:10:30 INFO mapred.FileInputFormat: Total input paths to process : 2
16/08/09 20:10:30 INFO mapreduce.JobSubmitter: number of splits:2
16/08/09 20:10:31 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1470792183416_0001
16/08/09 20:10:32 INFO impl.YarnClientImpl: Submitted application application_1470792183416_0001
16/08/09 20:10:32 INFO mapreduce.Job: The url to track the job: http://NN01.HadoopVM:8088/proxy/application_1470792183416_0001/
16/08/09 20:10:32 INFO mapreduce.Job: Running job: job_1470792183416_0001
16/08/09 20:10:45 INFO mapreduce.Job: Job job_1470792183416_0001 running in uber mode : false
16/08/09 20:10:45 INFO mapreduce.Job:  map 0% reduce 0%
16/08/09 20:10:59 INFO mapreduce.Job:  map 50% reduce 0%
16/08/09 20:11:03 INFO mapreduce.Job:  map 100% reduce 0%
16/08/09 20:11:05 INFO mapreduce.Job:  map 100% reduce 100%
16/08/09 20:11:06 INFO mapreduce.Job: Job job_1470792183416_0001 completed successfully
16/08/09 20:11:06 INFO mapreduce.Job: Counters: 49
	File System Counters
		FILE: Number of bytes read=47
		FILE: Number of bytes written=330444
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=223
		HDFS: Number of bytes written=25
		HDFS: Number of read operations=9
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=2
	Job Counters
		Launched map tasks=2
		Launched reduce tasks=1
		Data-local map tasks=2
		Total time spent by all maps in occupied slots (ms)=24400
		Total time spent by all reduces in occupied slots (ms)=3381
		Total time spent by all map tasks (ms)=24400
		Total time spent by all reduce tasks (ms)=3381
		Total vcore-milliseconds taken by all map tasks=24400
		Total vcore-milliseconds taken by all reduce tasks=3381
		Total megabyte-milliseconds taken by all map tasks=24985600
		Total megabyte-milliseconds taken by all reduce tasks=3462144
	Map-Reduce Framework
		Map input records=2
		Map output records=4
		Map output bytes=33
		Map output materialized bytes=53
		Input split bytes=198
		Combine input records=0
		Combine output records=0
		Reduce input groups=3
		Reduce shuffle bytes=53
		Reduce input records=4
		Reduce output records=3
		Spilled Records=8
		Shuffled Maps =2
		Failed Shuffles=0
		Merged Map outputs=2
		GC time elapsed (ms)=1505
		CPU time spent (ms)=6830
		Physical memory (bytes) snapshot=677711872
		Virtual memory (bytes) snapshot=6261100544
		Total committed heap usage (bytes)=482869248
	Shuffle Errors
		BAD_ID=0
		CONNECTION=0
		IO_ERROR=0
		WRONG_LENGTH=0
		WRONG_MAP=0
		WRONG_REDUCE=0
	File Input Format Counters
		Bytes Read=25
	File Output Format Counters
		Bytes Written=25
16/08/09 20:11:06 INFO streaming.StreamJob: Output directory: /words/py-output
	`

### 3 HBase

#### Introduction
[Apache Hbase](http://hbase.apache.org/book.html#quickstart)安装及测试

提供海量数据存储功能，是一种构建在HDFS之上的分布式、面向列的存储系统。

>> Browse to the Web UI

* connect to the UI for the Master[H1:16010](H1:16010) or [H2:16010](H2:16010)

#### The Apache HBase Shell演示

* 1 [Scripting with Ruby](http://hbase.apache.org/book.html#scripting)
* 2 [Running the Shell in Non-Interactive Mode](http://hbase.apache.org/book.html#_running_the_shell_in_non_interactive_mode)
* 3 [HBase Shell in OS Scripts](http://hbase.apache.org/book.html#hbase.shell.noninteractive)
* 4 [Read HBase Shell Commands from a Command File](http://hbase.apache.org/book.html#_read_hbase_shell_commands_from_a_command_file)

#### [Data Model](http://hbase.apache.org/book.html#datamodel)





## 2 Service Programming

### 1 Zookeeper

![](/images/zookeeper_small.gif)

[Apache Zookeeper](https://zookeeper.apache.org/)

### 2 kafka

一个分布式的、分区的、多副本的实时消息发布和订阅系统。提供可扩展、高吞吐、低延迟、高可靠的消息分发服务。

[quickstart](http://kafka.apache.org/documentation.html#quickstart)

视频教程[http://www.jikexueyuan.com/course/kafka/](http://www.jikexueyuan.com/course/kafka/)

* > 1 Introduction

[Introduction](http://kafka.apache.org/documentation.html#introduction)

集群实现与演示

	`
[hadoop@NN01 ~]$ ~/tools/runRemoteCmd.sh "/home/hadoop/kafka/bin/kafka-server-start.sh $KAFKA_HOME/config/server.properties &" all

[hadoop@DN02 bin]$ $KAFKA_HOME/bin/kafka-console-producer.sh --broker-list NN01.HadoopVM:9092 --sync --topic test

[hadoop@DN01 bin]$ $KAFKA_HOME/bin/kafka-console-consumer.sh --zookeeper NN01.HadoopVM:2181 --topic test --from-beginning
	`

## 3 Distributed Programming

### 1 Pig

### 2 Hive

[Hive Install](https://cwiki.apache.org/confluence/display/Hive/GettingStarted#GettingStarted-InstallationandConfiguration)

[Hive metastore三种配置方式](http://blog.csdn.net/reesun/article/details/8556078)

建立在Hadoop基础上的开源的数据仓库，提供类似SQL的Hive QL语言操作结构化数据存储服务和基本的数据分析服务。

[Running Hive](https://cwiki.apache.org/confluence/display/Hive/GettingStarted#GettingStarted-RunningHive)




### 3 Spark

基于内存进行计算的分布式计算框架。

#### Spark Overview

Apache Spark is a fast and general-purpose cluster computing system. It provides high-level APIs in Java, Scala, Python and R, and an optimized engine that supports general execution graphs. It also supports a rich set of higher-level tools including [Spark SQL](http://spark.apache.org/docs/latest/sql-programming-guide.html) for SQL and structured data processing, [MLlib](http://spark.apache.org/docs/latest/ml-guide.html) for machine learning, [GraphX](http://spark.apache.org/docs/latest/graphx-programming-guide.html) for graph processing, and [Spark Streaming](http://spark.apache.org/docs/latest/streaming-programming-guide.html).

Spark的适用场景

Spark是基于内存的迭代计算框架，适用于需要多次操作特定数据集的应用场合。需要反复操作的次数越多，所需读取的数据量越大，受益越大，数据量小但是计算密集度较大的场合，受益就相对较小
由于RDD的特性，Spark不适用那种异步细粒度更新状态的应用，例如web服务的存储或者是增量的web爬虫和索引。就是对于那种增量修改的应用模型不适合。
总的来说Spark的适用面比较广泛且比较通用。

运行模式
本地模式
Standalone模式
Mesos模式
yarn模式

* > 1 Cluster Manager Types

The system currently supports three cluster managers:

>> 1 Standalone – a simple cluster manager included with Spark that makes it easy to set up a cluster.

>> 2 Apache Mesos – a general cluster manager that can also run Hadoop MapReduce and service applications.

>> 3 Hadoop YARN – the resource manager in Hadoop 2.


#### Machine Learning Library (MLlib) Guide[refer](http://spark.apache.org/docs/latest/ml-guide.html)

[Spark快速入门指南 – Spark安装与基础使用](http://dblab.xmu.edu.cn/blog/spark-quick-start-guide/)

	`
	[hadoop@NN01 sparkapp]$ spark-submit --class "SimpleApp" ~/sparkapp/target/scala-2.11/simple-project_2.11-1.0.jar 2>&1|grep "Line"
Lines with a: 4, Lines with b: 2
	`

## 4 Machine Learning

### 1 Mahout

## 5 Data Ingestion

### 1 Storm
![](/images/storm-flow.png)
[Setting up a Storm Cluster](http://storm.apache.org/releases/1.0.1/Setting-up-a-Storm-cluster.html)

[Apache Storm 的历史及经验教训](http://www.oschina.net/translate/history-of-apache-storm-and-lessons-learned?cmp&p=4#)

[Storm Index](http://storm.apache.org/releases/1.0.1/index.html)

[Talks and Slideshows](http://storm.apache.org/talksAndVideos.html)

#### 命名方式

>> Storm暴风雨：其组件大多也以气象名词命名
>>
>> spout龙卷：形象的理解是把原始数据卷进Storm流式计算中
>>
>> bolt雷电：从spout或者其他bolt中接收数据进行处理或者输出
>>
>> nimbus雨云：主控节点，存在单点问题，不过可以用watchdog来保证其可用性，fast-fail后马上就启动
>>
>> topology拓扑：Storm的任务单元，形象的理解拓扑的点是spout或者bolt，之间的数据流是线，整个构成一个拓扑




### 2 Sqoop



## 6 System Deployment

### 1 Ambari


