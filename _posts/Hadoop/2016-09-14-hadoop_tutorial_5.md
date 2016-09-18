---
layout: post
title: Hadoop 集群安装 快速指南
category: Hadoop
catalog: yes
description: Hadoop 安装
tags:
    - Big Data
    - 大数据
---

## Prerequisites

3台虚拟机
SSH免密登陆
Java已安装
Hadoop安装包已经下载
tools工具已经下载

## 配置

配置分配目录：

目录|说明
hdfs|存放hdfs相关文件，一般存放namenode、datanode、logs信息文件
logs|存放启动日志信息
tmp|存放hdfs系统临时文件等信息
hdfs/name|存放namenode信息
hdfs/logs|存放hdfs日志信息
hdfs/data|存放datanode信息


使用hadoop用户身份，以安装2.7.3为例：

### 1 建立以上目录

~~~shell
[hadoop@NN01 hadoop]$ cd ~/hadoop-2.7.3/
[hadoop@NN01 hadoop-2.7.3]$ ls
bin  etc  include  lib  libexec  LICENSE.txt  NOTICE.txt  README.txt  sbin  share
[hadoop@NN01 hadoop-2.7.3]$ ~/tools/runRemoteCmd.sh "mkdir ~/hadoop-2.7.3/hdfs ~/hadoop-2.7.3/logs ~/hadoop-2.7.3/tmp" all
**************NN01.HadoopVM************
**************DN01.HadoopVM************
**************DN02.HadoopVM************
[hadoop@NN01 hadoop-2.7.3]$ ~/tools/runRemoteCmd.sh "mkdir -p ~/hadoop-2.7.3/hdfs/data hadoop-2.7.3/hdfs/logs  hadoop-2.7.3/hdfs/name" all
**************NN01.HadoopVM************
**************DN01.HadoopVM************
**************DN02.HadoopVM************
[hadoop@NN01 hadoop-2.7.3]$ ls
bin  etc  hdfs  include  lib  libexec  LICENSE.txt  logs  NOTICE.txt  README.txt  sbin  share  tmp
[hadoop@NN01 hadoop-2.7.3]$ ls hdfs
data  logs  name
~~~

### 2 修改配置文件

Hadoop’s Java configuration is driven by two types of important configuration files:

* Read-only default configuration - core-default.xml, hdfs-default.xml, yarn-default.xml and mapred-default.xml.
* Site-specific configuration - etc/hadoop/core-site.xml, etc/hadoop/hdfs-site.xml, etc/hadoop/yarn-site.xml and etc/hadoop/mapred-site.xml.

Additionally, you can control the Hadoop scripts found in the bin/ directory of the distribution, by setting site-specific values via the etc/hadoop/hadoop-env.sh and etc/hadoop/yarn-env.sh.

To configure the Hadoop cluster you will need to configure the environment in which the Hadoop daemons execute as well as the configuration parameters for the Hadoop daemons.

HDFS daemons are NameNode, SecondaryNameNode, and DataNode. YARN damones are ResourceManager, NodeManager, and WebAppProxy. If MapReduce is to be used, then the MapReduce Job History Server will also be running. For large installations, these are generally running on separate hosts.

**注意：**
修改配置前将hadoop-2.7.3软链接为hadoop

~~~shell
[hadoop@NN01 ~]$ ./tools/runRemoteCmd.sh "ln -s hadoop-2.7.3 hadoop" all
~~~

#### 2.1 修改etc/hadoop/core-site.xml

Parameter  | Value  | Notes
fs.defaultFS  |  NameNode URI  |  hdfs://host:port/
io.file.buffer.size |131072 | Size of read/write buffer used in SequenceFiles.

~~~
<!-- Put site-specific property overrides in this file. -->

<configuration>
  <property>
      <name>fs.default.name</name>
      <value>hdfs://NN01.HadoopVM:9000</value>
      <final>true</final>
  </property>

  <property>
      <name>hadoop.tmp.dir</name>
      <value>file:///home/hadoop/hadoop/tmp</value>
      </property>

  <property>
      <name>ds.default.name</name>
      <value>hdfs://NN01.HadoopVM:54310</value>
      <final>true</final>
  </property>
  <property>
      <name>ha.zookeeper.quorum</name>
      <value>NN01.HadoopVM:2181,DN01.HadoopVM:2181,DN02.HadoopVM:2181</value>
  </property>
</configuration>
~~~

#### 2.2 修改etc/hadoop/hdfs-site.xml

**Configurations for NameNode:**

Parameter|   Value |  Notes
dfs.namenode.name.dir |  Path on the local filesystem where the NameNode stores the namespace and transactions logs persistently.  |  If this is a comma-delimited list of directories then the name table is replicated in all of the directories, for redundancy.
dfs.hosts / dfs.hosts.exclude  | List of permitted/excluded DataNodes.  | If necessary, use these files to control the list of allowable datanodes.
dfs.blocksize  | 268435456  | HDFS blocksize of 256MB for large file-systems.
dfs.namenode.handler.count | 100 |More NameNode server threads to handle RPCs from large number of DataNodes.

**Configurations for DataNode:**

Parameter |  Value  | Notes|
dfs.datanode.data.dir |  Comma separated list of paths on the local filesystem of a DataNode where it should store its blocks.  | If this is a comma-delimited list of directories, then data will be stored in all named directories, typically on different devices.

~~~
<!-- Put site-specific property overrides in this file. -->

<configuration>
  <property>
      <name>dfs.namenode.name.dir</name>
      <value>file:/home/hadoop/hadoop/hdfs/name</value>
      <final>true</final>
  </property>

  <property>
      <name>dfs.datanode.data.dir</name>
      <value>file:/home/hadoop/hadoop/hdfs/data</value>
      <final>true</final>
  </property>
  <property>
      <name>dfs.replication</name>
      <value>2</value>
  </property>
</configuration>
~~~

#### 2.3 修改etc/hadoop/yarn-site.xml

**Configurations for ResourceManager and NodeManager:**

Parameter   |   Value   |   Notes
yarn.acl.enable |   true /false |   Enable ACLs? Defaults to false.
yarn.admin.acl  |   Admin ACL   |   ACL to set admins on the cluster. ACLs are of for comma-separated-usersspacecomma-separated-groups. Defaults to special value of * which meansanyone. Special value of just space means no one has access.
yarn.log-aggregation-enable |   FALSE   |   Configuration to enable or disable log aggregation

**Configurations for ResourceManager:**

Parameter   |   Value   |   Notes
yarn.resourcemanager.address    |   ResourceManager host:port for clients to submit jobs.   |   host:port If set, overrides the hostname set in yarn.resourcemanager.hostname.
yarn.resourcemanager.scheduler.address  |   ResourceManager host:port for ApplicationMasters to talk to Scheduler to obtain resources.  |   host:port If set, overrides the hostname set in yarn.resourcemanager.hostname.
yarn.resourcemanager.resource-tracker.address   |   ResourceManager host:port for NodeManagers. |   host:port If set, overrides the hostname set in yarn.resourcemanager.hostname.
yarn.resourcemanager.admin.address  |   ResourceManager host:port for administrative commands.  |   host:port If set, overrides the hostname set in yarn.resourcemanager.hostname.
yarn.resourcemanager.webapp.address |   ResourceManager web-ui host:port.   |   host:port If set, overrides the hostname set in yarn.resourcemanager.hostname.
yarn.resourcemanager.hostname   |   ResourceManager host.   |   host Single hostname that can be set in place of setting all yarn.resourcemanager*addressresources. Results in default ports for ResourceManager components.
yarn.resourcemanager.scheduler.class    |   ResourceManager Scheduler class.    |   CapacityScheduler (recommended), FairScheduler (also recommended), or FifoScheduler
yarn.scheduler.minimum-allocation-mb    |   Minimum limit of memory to allocate to each container request at the Resource Manager.  |   In MBs
yarn.scheduler.maximum-allocation-mb    |   Maximum limit of memory to allocate to each container request at the Resource Manager.  |   In MBs
yarn.resourcemanager.nodes.include-path /yarn.resourcemanager.nodes.exclude-path    |   List of permitted/excluded NodeManagers.    |   If necessary, use these files to control the list of allowable NodeManagers.

**Configurations for NodeManager:**

Parameter   |   Value   |   Notes
yarn.nodemanager.resource.memory-mb |   Resource i.e. available physical memory, in MB, for given NodeManager   |   Defines total available resources on the NodeManager to be made available to running containers
yarn.nodemanager.vmem-pmem-ratio    |   Maximum ratio by which virtual memory usage of tasks may exceed physical memory |   The virtual memory usage of each task may exceed its physical memory limit by this ratio. The total amount of virtual memory used by tasks on the NodeManager may exceed its physical memory usage by this ratio.
yarn.nodemanager.local-dirs |   Comma-separated list of paths on the local filesystem where intermediate data is written.   |   Multiple paths help spread disk i/o.
yarn.nodemanager.log-dirs   |   Comma-separated list of paths on the local filesystem where logs are written.   |   Multiple paths help spread disk i/o.
yarn.nodemanager.log.retain-seconds |   10800   |   Default time (in seconds) to retain log files on the NodeManager Only applicable if log-aggregation is disabled.
yarn.nodemanager.remote-app-log-dir |   /logs   |   HDFS directory where the application logs are moved on application completion. Need to set appropriate permissions. Only applicable if log-aggregation is enabled.
yarn.nodemanager.remote-app-log-dir-suffix  |   logs    |   Suffix appended to the remote log dir. Logs will be aggregated to ${yarn.nodemanager.remote-app-log-dir}/${user}/${thisParam} Only applicable if log-aggregation is enabled.
yarn.nodemanager.aux-services   |   mapreduce_shuffle   |   Shuffle service that needs to be set for Map Reduce applications.

~~~shell
<!-- Site specific YARN configuration properties -->
<configuration>
  <property>
      <name>yarn.nodemanager.aux-services</name>
      <value>mapreduce_shuffle</value>
  </property>

  <property>
      <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
      <value>org.apache.hadoop.mapred.ShuffleHandler</value>
  </property>

  <property>
      <name>yarn.resourcemanager.hostname</name>
      <value>NN01.HadoopVM</value>
  </property>
  <property>
      <name>yarn.resourcemanager.address</name>
      <value>NN01.HadoopVM:8032</value>
  </property>
  <property>
      <name>yarn.resourcemanager.scheduler.address</name>
      <value>NN01.HadoopVM:8030</value>
  </property>
      <property>
      <name>yarn.resourcemanager.resource-tracker.address</name>
      <value>NN01.HadoopVM:8031</value>
  </property>
  <property>
      <name>yarn.resourcemanager.admin.address</name>
      <value>NN01.HadoopVM:8033</value>
  </property>
  <property>
      <name>yarn.resourcemanager.webapp.address</name>
      <value>NN01.HadoopVM:8088</value>
  </property>
</configuration>
~~~


#### 2.4 修改etc/hadoop/mapred-site.xml

**Configurations for MapReduce Applications:**

Parameter   |   Value   |   Notes
mapreduce.framework.name    |   yarn    |   Execution framework set to Hadoop YARN.
mapreduce.map.memory.mb |   1536    |   Larger resource limit for maps.
mapreduce.map.java.opts |   -Xmx1024M   |   Larger heap-size for child jvms of maps.
mapreduce.reduce.memory.mb  |   3072    |   Larger resource limit for reduces.
mapreduce.reduce.java.opts  |   -Xmx2560M   |   Larger heap-size for child jvms of reduces.
mapreduce.task.io.sort.mb   |   512 |   Higher memory-limit while sorting data for efficiency.
mapreduce.task.io.sort.factor   |   100 |   More streams merged at once while sorting files.
mapreduce.reduce.shuffle.parallelcopies |   50  |   Higher number of parallel copies run by reduces to fetch outputs from very large number of maps.

**Configurations for MapReduce JobHistory Server:**

Parameter   |   Value   |   Notes
mapreduce.jobhistory.address    |   MapReduce JobHistory Server host:port   |   Default port is 10020.
mapreduce.jobhistory.webapp.address |   MapReduce JobHistory Server Web UI host:port    |   Default port is 19888.
mapreduce.jobhistory.intermediate-done-dir  |   /mr-history/tmp |   Directory where history files are written by MapReduce jobs.
mapreduce.jobhistory.done-dir   |   /mr-history/done    |   Directory where history files are managed by the MR JobHistory Server.

~~~
<!-- Put site-specific property overrides in this file. -->

<configuration>
   <property>
          <name>mapreduce.framework.name</name>
          <value>yarn</value>
   </property>
   <property>
          <name>mapreduce.jobhistory.address</name>
          <value>NN01.HadoopVM:10020</value>
   </property>
   <property>
          <name>mapreduce.jobhistory.webapp.address</name>
          <value>NN01.HadoopVM:19888</value>
   </property>
</configuration>
~~~

#### 2.5 修改masters slaves

~~~
[hadoop@NN01 hadoop]$ cat masters
NN01.HadoopVM
[hadoop@NN01 hadoop]$ cat slaves
NN01.HadoopVM

DN01.HadoopVM
DN02.HadoopVM
~~~

#### 2.6 增加执行权限

~~~
[hadoop@NN01 hadoop]$ chmod +x *.sh
~~~

#### 2.7 环境配置

~~~
export JAVA_HOME=/opt/tool/jdk1.8
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export HADOOP_HOME=/home/hadoop/hadoop
export PATH=$PATH:$HADOOP_HOME/bin
export HADOOP_LOG_DIR=/home/hadoop/hadoop/logs
export YARN_LOG_DIR=$HADOOP_LOG_DIR
export HADOOP_PREFIX=$HADOOP_HOME
export HADOOP_CONF_DIR=/home/hadoop/hadoop/etc/hadoop
export HADOOP_YARN_HOME=$HADOOP_HOME
export HADOOP_CLASSPATH=$JAVA_HOME/lib/tools.jar
~~~

#### 2.8 修改yarn-env.sh

~~~
export JAVA_HOME=/opt/tool/jdk1.8
~~~

### 3 Hadoop Startup

The first time you bring up HDFS, it must be formatted. Format a new distributed filesystem as hdfs:

~~~
[hdfs]$ $HADOOP_PREFIX/bin/hdfs namenode -format <cluster_name>
~~~

Start HDFS AND YARN

~~~
[hadoop@NN01 sbin]$ ./start-dfs.sh
Starting namenodes on [NN01.HadoopVM]
NN01.HadoopVM: starting namenode, logging to /home/hadoop/hadoop/logs/hadoop-hadoop-namenode-NN01.HadoopVM.out
DN01.HadoopVM: starting datanode, logging to /home/hadoop/hadoop/logs/hadoop-hadoop-datanode-DN01.HadoopVM.out
NN01.HadoopVM: starting datanode, logging to /home/hadoop/hadoop/logs/hadoop-hadoop-datanode-NN01.HadoopVM.out
DN02.HadoopVM: starting datanode, logging to /home/hadoop/hadoop/logs/hadoop-hadoop-datanode-DN02.HadoopVM.out
Starting secondary namenodes [0.0.0.0]
0.0.0.0: starting secondarynamenode, logging to /home/hadoop/hadoop/logs/hadoop-hadoop-secondarynamenode-NN01.HadoopVM.out
[hadoop@NN01 sbin]$ ./start-yarn.sh
starting yarn daemons
starting resourcemanager, logging to /home/hadoop/hadoop/logs/yarn-hadoop-resourcemanager-NN01.HadoopVM.out
NN01.HadoopVM: starting nodemanager, logging to /home/hadoop/hadoop/logs/yarn-hadoop-nodemanager-NN01.HadoopVM.out
DN01.HadoopVM: starting nodemanager, logging to /home/hadoop/hadoop/logs/yarn-hadoop-nodemanager-DN01.HadoopVM.out
DN02.HadoopVM: starting nodemanager, logging to /home/hadoop/hadoop/logs/yarn-hadoop-nodemanager-DN02.HadoopVM.out
~~~

Start the MapReduce JobHistory Server with the following command, run on the designated server as mapred:

~~~
[hadoop@NN01 sbin]$ $HADOOP_PREFIX/sbin/mr-jobhistory-daemon.sh --config $HADOOP_CONF_DIR start historyserver
starting historyserver, logging to /home/hadoop/hadoop/logs/mapred-hadoop-historyserver-NN01.HadoopVM.out
~~~

Web Interfaces

Once the Hadoop cluster is up and running check the web-ui of the components as described below:

Daemon|  Web Interface |Notes
NameNode | http://nn_host:port/ | Default HTTP port is 50070.
ResourceManager |http://rm_host:port/  |Default HTTP port is 8088.
MapReduce JobHistory Server |http://jhs_host:port/ |Default HTTP port is 19888.

### 4 Hadoop Shutdown

Stop HDFS AND YARN

~~~
[hadoop@NN01 sbin]$ ./stop-dfs.sh
Stopping namenodes on [NN01.HadoopVM]
NN01.HadoopVM: stopping namenode
NN01.HadoopVM: stopping datanode
DN01.HadoopVM: stopping datanode
DN02.HadoopVM: stopping datanode
Stopping secondary namenodes [0.0.0.0]
0.0.0.0: stopping secondarynamenode
[hadoop@NN01 sbin]$ ./stop-yarn.sh
stopping yarn daemons
stopping resourcemanager
NN01.HadoopVM: stopping nodemanager
DN01.HadoopVM: stopping nodemanager
DN02.HadoopVM: stopping nodemanager
~~~

Stop the MapReduce JobHistory Server with the following command, run on the designated server as mapred:

~~~
[hadoop@NN01 sbin]$ $HADOOP_PREFIX/sbin/mr-jobhistory-daemon.sh --config $HADOOP_CONF_DIR stop historyserver
stopping historyserver
~~~

### 5 Hadoop版本切换

按照上述配置，配置另一套Hadoop环境于相同节点上。

**查看目前版本状态**

![](/images/hadoop/hadoop2.7.3.png)

**切换**

即将软链接改为指向2.6.4即可

~~~shell
[hadoop@NN01 ~]$ ./tools/runRemoteCmd.sh "rm ~/hadoop" all
**************NN01.HadoopVM************
**************DN01.HadoopVM************
**************DN02.HadoopVM************
[hadoop@NN01 ~]$ ./tools/runRemoteCmd.sh "ln -s ~/hadoop2.6 ~/hadoop" all
**************NN01.HadoopVM************
**************DN01.HadoopVM************
**************DN02.HadoopVM************
~~~

**查看目前版本状态**

![](/images/hadoop/hadoop2.6.4.png)

### 6 Other

配置SecondaryNameNode

~~~
[hadoop@NN01 hadoop]$ tail etc/hadoop/hdfs-site.xml
...
  <property>
    <name>dfs.namenode.secondary.http-address</name>
    <value>DN01.HadoopVM:50090</value>
  </property>
[hadoop@NN01 hadoop]$ ./sbin/start-dfs.sh
Starting namenodes on [NN01.HadoopVM]
NN01.HadoopVM: starting namenode, logging to /home/hadoop/hadoop/logs/hadoop-hadoop-namenode-NN01.HadoopVM.out
NN01.HadoopVM: starting datanode, logging to /home/hadoop/hadoop/logs/hadoop-hadoop-datanode-NN01.HadoopVM.out
DN02.HadoopVM: starting datanode, logging to /home/hadoop/hadoop/logs/hadoop-hadoop-datanode-DN02.HadoopVM.out
DN01.HadoopVM: starting datanode, logging to /home/hadoop/hadoop/logs/hadoop-hadoop-datanode-DN01.HadoopVM.out
Starting secondary namenodes [DN01.HadoopVM]
DN01.HadoopVM: starting secondarynamenode, logging to /home/hadoop/hadoop/logs/hadoop-hadoop-secondarynamenode-DN01.HadoopVM.out
[hadoop@DN01 current]$ jps
4504 DataNode
4570 SecondaryNameNode
~~~

故障恢复

The latest checkpoint can be imported to the NameNode if all other copies of the image and the edits files are lost. In order to do that one should:

* Create an empty directory specified inthedfs.namenode.name.dir configuration variable;
* Specify the location of the checkpointdirectory in the configuration variabledfs.namenode.checkpoint.dir;
*  and start the NameNode with-importCheckpoint option.

The NameNode will upload the checkpoint from the dfs.namenode.checkpoint.dir directory and then save it to the NameNode directory(s) set in dfs.namenode.name.dir. The NameNodewill fail if a legal image is contained in dfs.namenode.name.dir. The NameNode verifies that the image indfs.namenode.checkpoint.dir is consistent, but does not modify it in any way.

~~~
[hadoop@NN01 hdfs]$ $HADOOP_PREFIX/sbin/hadoop-daemon.sh --config $HADOOP_CONF_DIR --script hdfs start namenode -importCheckpoint
starting namenode, logging to /home/hadoop/hadoop/logs/hadoop-hadoop-namenode-NN01.HadoopVM.out
[hadoop@NN01 hdfs]$ jps
11571 Jps
11530 NameNode
[hadoop@NN01 hdfs]$ $HADOOP_PREFIX/sbin/hadoop-daemons.sh --config $HADOOP_CONF_DIR --script hdfs start datanode
DN01.HadoopVM: starting datanode, logging to /home/hadoop/hadoop/logs/hadoop-hadoop-datanode-DN01.HadoopVM.out
DN02.HadoopVM: starting datanode, logging to /home/hadoop/hadoop/logs/hadoop-hadoop-datanode-DN02.HadoopVM.out
NN01.HadoopVM: starting datanode, logging to /home/hadoop/hadoop/logs/hadoop-hadoop-datanode-NN01.HadoopVM.out
~~~
