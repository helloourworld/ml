---
layout: post
title: Hadoop Family
category: MLAdvance
description:  Hadoop Family. Hadoop 生态圈学习与体会。
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

### 2 MapReduce


