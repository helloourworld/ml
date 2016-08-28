---
layout: post
title: 培训虚拟机使用指南
category: Hadoop
catalog: yes
description: 使用指南
tags:
    - Big Data
    - 大数据
---
##   开启虚拟机

确保安装了VMware软件。打开虚拟机后查看IP并更改hosts。注意：请根据本地的IP来更改，查看本地ip方法：ifconfig命令查看。默认NN01为~128；DN01为129；DN02为130。

~~~
Last login: Tue Aug 23 18:47:50 2016 from 192.168.71.1
[hadoop@NN01 ~]$ sudo vim /etc/hosts
[hadoop@NN01 ~]$ sudo tools/deploy.sh /etc/hosts /etc/hosts slave
****DN01.HadoopVM*****
Warning: Permanently added the RSA host key for IP address '192.168.71.134' to the list of known hosts.
hosts                                 100%  293     0.3KB/s   00:00
****DN02.HadoopVM*****
Warning: Permanently added the RSA host key for IP address '192.168.71.133' to the list of known hosts.
hosts                                 100%  293     0.3KB/s   00:00
[hadoop@NN01 ~]$
~~~

## 开启服务

你可以执行以下脚本来启动，也可以一个个单独启动。

~~~
[hadoop@NN01 ~]$ ./start_up.sh
~~~

一个个单独启动

~~~
# start up the hadoop servers

# start up hdfs
$HADOOP_HOME/sbin/start-all.sh && sleep 120 &
$HADOOP_HOME/sbin/mr-jobhistory-daemon.sh start historyserver --config $HADOOP_CONF_DIR && sleep 90 &

# start up zookeeper
~/zookeeper.sh start && exec sleep 90 &

# start up kafka
# ~/tools/runRemoteCmd.sh "/home/hadoop/kafka/bin/kafka-server-start.sh $KAFKA_HOME/config/server.properties && sleep 3 && kill -2 $!" all

# start up hbase
~/hbase/bin/start-hbase.sh &

~/zookeeper.sh status
~~~

~~~
# start up spark
[hadoop@NN01 ~]$ cd spark
[hadoop@NN01 spark]$ ./sbin/start-all.sh
~~~

spark启动后可在8080端口查看__![](/images/hadoop/spark_ui.png)


