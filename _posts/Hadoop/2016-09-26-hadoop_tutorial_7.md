---
layout: post
title: Zeppelin安装 快速指南
category: Hadoop
catalog: yes
description: Zeppelin 安装
tags:
    - Big Data
    - 大数据
---

## Zeppelin Installation

Stable binary package, please visit [Apache Zeppelin download Page](http://zeppelin.apache.org/download.html).

**[Start Apache Zeppelin with a service manager](http://zeppelin.apache.org/docs/0.6.1/install/install.html#optional-start-apache-zeppelin-with-a-service-manager)**

**zeppelin.server and port modify:**

~~~
 22 <property>
 23   <name>zeppelin.server.addr</name>
 24   <value>NN01.HadoopVM</value>
 25   <description>Server address</description>
 26 </property>
 27
 28 <property>
 29   <name>zeppelin.server.port</name>
 30   <value>9090</value>
 31   <description>Server port.</description>
 32 </property>
~~~

## HIve Interpreter for Zeppelin

**Importtant Notice**

```
Hive Interpreter will be deprecated and merged into JDBC Interpreter. You can use Hive Interpreter by using JDBC Interpreter with same functionality. See the example below of settings and dependencies.
```
**Properties**

Property  |  Value
hive.driver |org.apache.hive.jdbc.HiveDriver
hive.url  |  jdbc:hive2://localhost:10000/;auth=noSasl
hive.user |  hiveUser
hive.password |  hivePassword

![](/images/hadoop/beeline_jdbc_connect_with_nosasl.png)

**注意**: 只有在hive-site.xml中改动authMechanism认证时才需要加/;auth=noSasl

## Spark Interpreter for Apache Zeppelin

Apache Spark is a fast and general-purpose cluster computing system. It provides high-level APIs in Java, Scala, Python and R, and an optimized engine that supports general execution graphs Apache Spark is supported in Zeppelin with Spark Interpreter group, which consists of five interpreters.

Name    |   Class   |   Description
%spark  |   SparkInterpreter    |   Creates a SparkContext and provides a scala environment
%pyspark    |   PySparkInterpreter  |   Provides a python environment
%r  |   SparkRInterpreter   |   Provides an R environment with SparkR support
%sql    |   SparkSQLInterpreter |   Provides a SQL environment
%dep    |   DepInterpreter  |   Dependency loader

Export SPARK_HOME

In conf/zeppelin-env.sh, export SPARK_HOME environment variable with your Spark installation path.

for example

~~~
export SPARK_HOME=/usr/lib/spark
~~~

## Bugs
1 Hive Connect

~~~
Could not open client transport with JDBC Uri: jdbc:hive2://NN01.HadoopVM:10000: java.net.ConnectException: Connection refused
~~~

Modify


2 Jackson version is too old 2.5.3

~~~
java.lang.NoClassDefFoundError: Could not initialize class org.apache.spark.rdd.RDDOperationScope$
  at org.apache.spark.SparkContext.withScope(SparkContext.scala:682)
  at org.apache.spark.SparkContext.textFile(SparkContext.scala:800)
~~~

Modify

~~~
# Apache Spark2.0 依赖的jackson相关文件版本为2.6.5(参考 spark的pom.xml文件)
# Apache Zeppelin 在启动时会加载${ZEPPELIN_HOME}/lib下面的类库。使用${SPARK_HOME}/jars/下的替换。
# Debug

[hadoop@NN01 jars]$ cp jackson-core-2.6.5.jar ~/zeppelin/lib/.
[hadoop@NN01 jars]$ cp jackson-annotations-2.6.5.jar ~/zeppelin/lib/.
[hadoop@NN01 jars]$ cp jackson-databind-2.6.5.jar ~/zeppelin/lib/.
~~~

~~~Zeppelin
%spark
val bankText = sc.textFile("file:///home/hadoop/bankfull.csv")
~~~
