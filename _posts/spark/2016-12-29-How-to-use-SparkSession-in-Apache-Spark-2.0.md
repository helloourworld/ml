---
layout: post
title: How to use SparkSession in Apache Spark 2.0
category: Spark
catalog: yes
description: How to use SparkSession in Apache Spark 2.0. A unified entry point for manipulating data with Spark
tags:
    - Spark
    - Python
---
## pre

This blog is from here. https://databricks.com/blog/2016/08/15/how-to-use-sparksession-in-apache-spark-2-0.html

Generally, a session is an interaction between two or more entities. In computer parlance, its usage is prominent in the realm of networked computers on the internet. First with TCP session, then with login session, followed by HTTP and user session, so no surprise that we now have SparkSession, introduced in Apache Spark 2.0.

Beyond a time-bounded interaction, SparkSession provides a single point of entry to interact with underlying Spark functionality and allows programming Spark with DataFrame and Dataset APIs. Most importantly, it curbs the number of concepts and constructs a developer has to juggle while interacting with Spark.

In this blog and its accompanying Databricks notebook, we will explore SparkSession functionality in Spark 2.0.

## Exploring SparkSession’s Unified Functionality

First, we will examine a Spark application, SparkSessionZipsExample, that reads zip codes from a JSON file and do some analytics using DataFrames APIs, followed by issuing Spark SQL queries, without accessing SparkContext, SQLContext or HiveContext.

## Creating a SparkSession

In previous versions of Spark, you had to create a SparkConf and SparkContext to interact with Spark, as shown here:

~~~
//set up the spark configuration and create contexts
val sparkConf = new SparkConf().setAppName("SparkSessionZipsExample").setMaster("local")
// your handle to SparkContext to access other context like SQLContext
val sc = new SparkContext(sparkConf).set("spark.some.config.option", "some-value")
val sqlContext = new org.apache.spark.sql.SQLContext(sc)
~~~

Whereas in Spark 2.0 the same effects can be achieved through SparkSession, without expliciting creating SparkConf, SparkContext or SQLContext, as they’re **encapsulated within the SparkSession**. Using a builder design pattern, it instantiates a SparkSession object if one does not already exist, along with its associated underlying contexts.

~~~scala
import org.apache.spark.sql.SparkSession
// Create a SparkSession. No need to create SparkContext
// You automatically get it as part of the SparkSession
val warehouseLocation = "file:${system:user.dir}/spark-warehouse"
val spark = SparkSession
   .builder()
   .appName("SparkSessionZipsExample")
   .config("spark.sql.warehouse.dir", warehouseLocation)
   .enableHiveSupport()
   .getOrCreate()
~~~

~~~python
from pyspark.sql import SparkSession

spark = SparkSession \
    .builder \
    .appName("Python Spark SQL basic example") \
    .config("spark.some.config.option", "some-value") \
    .getOrCreate()
~~~

~~~R
Sys.setenv(SPARK_HOME="/home/hadoop/spark")
.libPaths(c(file.path(Sys.getenv("SPARK_HOME"), "R", "lib"), .libPaths()))
library("SparkR")
sparkR.session()
sparkR.session(appName = "MyApp", sparkConfig = list(spark.some.config.option = "some-value"))
SparkR::sparkR.session()
# close connection
sparkR.session.stop()
~~~

**Another sloution**

My solution is:

* 1. Create a soft link of the SparkR directory in the the directory where other R packages are installed (ln -s /home/hadoop/spark/R/lib/SparkR /home/hadoop/R/x86_64-pc-linux-gnu-library/3.2)
* 2. Add only one line (Sys.setenv(SPARK_HOME=”/home/hadoop/spark”)) to the .Rprofile file.

At this point you can use the spark variable as your instance object to access its public methods and instances for the duration of your Spark job.

## Configuring Spark’s Runtime Properties

Once the SparkSession is instantiated, you can configure Spark’s runtime config properties. For example, in this code snippet, we can alter the existing runtime config options. Since configMap is a collection, you can use all of Scala’s iterable methods to access the data.

~~~
//set new runtime options
spark.conf.set("spark.sql.shuffle.partitions", 6)
spark.conf.set("spark.executor.memory", "2g")
//get all settings
val configMap:Map[String, String] = spark.conf.getAll()
~~~

## Accessing Catalog Metadata

Often, you may want to access and peruse the underlying catalog metadata. SparkSession exposes “catalog” as a public instance that contains methods that work with the metastore (i.e data catalog). Since these methods return a Dataset, you can use Dataset API to access or view data. In this snippet, we access table names and list of databases.

~~~
//fetch metadata data from the catalog
spark.catalog.listDatabases.show(false)
spark.catalog.listTables.show(false)
~~~

![Fig 1. Datasets returned from catalog](/images/spark/sparksession1.png)

## Creating Datasets and Dataframes

There are a number of ways to create DataFrames and Datasets using [SparkSession APIs](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.SparkSession)
One quick way to generate a Dataset is by using the spark.range method. When learning to manipulate [Dataset](http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.Dataset) with its API, this quick method proves useful. For example,

~~~scala
//create a Dataset using spark.range starting from 5 to 100, with increments of 5
val numDS = spark.range(5, 100, 5)
// reverse the order and display first 5 items
numDS.orderBy(desc("id")).show(5)
//compute descriptive stats and display them
numDs.describe().show()
// create a DataFrame using spark.createDataFrame from a List or Seq
val langPercentDF = spark.createDataFrame(List(("Scala", 35), ("Python", 30), ("R", 15), ("Java", 20)))
//rename the columns
val lpDF = langPercentDF.withColumnRenamed("_1", "language").withColumnRenamed("_2", "percent")
//order the DataFrame in descending order of percentage
lpDF.orderBy(desc("percent")).show(false)
~~~

~~~shell
scala> numDS.orderBy(desc("id")).show(5)
+---+
| id|
+---+
| 95|
| 90|
| 85|
| 80|
| 75|
+---+
only showing top 5 rows


scala> numDS.describe().show()
+-------+------------------+
|summary|                id|
+-------+------------------+
|  count|                19|
|   mean|              50.0|
| stddev|28.136571693556885|
|    min|                 5|
|    max|                95|
+-------+------------------+


scala> val langPercentDF = spark.createDataFrame(List(("Scala", 35), ("Python", 30), ("R", 15), ("Java", 20)))
langPercentDF: org.apache.spark.sql.DataFrame = [_1: string, _2: int]

scala> val lpDF = langPercentDF.withColumnRenamed("_1", "language").withColumnRenamed("_2", "percent")
lpDF: org.apache.spark.sql.DataFrame = [language: string, percent: int]

scala> //order the DataFrame in descending order of percentage

scala> lpDF.orderBy(desc("percent")).show(false)
+--------+-------+
|language|percent|
+--------+-------+
|Scala   |35     |
|Python  |30     |
|Java    |20     |
|R       |15     |
+--------+-------+
~~~

# Reading JSON Data with SparkSession API

Like any Scala object you can use spark, the SparkSession object, to access its public methods and instance fields. I can read JSON or CVS or TXT file, or I can read a parquet table. For example, in this code snippet, we will read a JSON file of zip codes, which returns a DataFrame, a collection of generic Rows.

~~~scala
// read the json file and create the dataframe
val jsonFile = args(0)
val zipsDF = spark.read.json(jsonFile)
//filter all cities whose population > 40K
zipsDF.filter(zipsDF.col("pop") > 40000).show(10)
~~~

# Using Spark SQL with SparkSession

Through SparkSession, you can access all of the Spark SQL functionality as you would through SQLContext. In the code sample below, we create a table against which we issue SQL queries.

~~~scala
// Now create an SQL table and issue SQL queries against it without
// using the sqlContext but through the SparkSession object.
// Creates a temporary view of the DataFrame
zipsDF.createOrReplaceTempView("zips_table")
zipsDF.cache()
val resultsDF = spark.sql("SELECT city, pop, state, zip FROM zips_table")
resultsDF.show(10)
~~~

~~~shell
scala> df2.filter(df2.col("age") >10 ).show(10)
+---+------+
|age|  name|
+---+------+
| 30|  Andy|
| 19|Justin|
+---+------+


scala> df2.createOrReplaceTempView("people")

scala> df2.cache()
res9: df2.type = [age: bigint, name: string]

scala> val resultDF = spark.sql("select name,age from people where age >10")
resultDF: org.apache.spark.sql.DataFrame = [name: string, age: bigint]

scala> resultDF.show()
+------+---+
|  name|age|
+------+---+
|  Andy| 30|
|Justin| 19|
+------+---+
~~~

## Saving and Reading from Hive table with SparkSession

Next, we are going to create a Hive table and issue queries against it using SparkSession object as you would with a HiveContext.

~~~scala
//drop the table if exists to get around existing table error
spark.sql("DROP TABLE IF EXISTS zips_hive_table")
//save as a hive table
spark.table("zips_table").write.saveAsTable("zips_hive_table")
//make a similar query against the hive table
val resultsHiveDF = spark.sql("SELECT city, pop, state, zip FROM zips_hive_table WHERE pop > 40000")
resultsHiveDF.show(10)
~~~
![Output from the Hive Table](/images/spark/sparksession3.png)

As you can observe, the results in the output runs from using the DataFrame API, Spark SQL and Hive queries are identical. You can access all sources and data, and how to run this example, from my github repo.
Second, let’s turn our attention to two Spark developer environments where the SparkSession is automatically created for you.

## SparkSession in Spark REPL and Databricks Notebook

First, as in previous versions of Spark, the spark-shell created a SparkContext (sc), so in Spark 2.0, the spark-shell creates a SparkSession (spark). In this spark-shell, you can see spark already exists, and you can view all its attributes.

~~~shell
scala> spark
res0: org.apache.spark.sql.SparkSession = org.apache.spark.sql.SparkSession@e06ec83

scala> spark.
baseRelationToDataFrame   conf              createDataset    emptyDataset   implicits         newSession   read         sparkContext   sqlContext   streams   udf
catalog                   createDataFrame   emptyDataFrame   experimental   listenerManager   range        readStream   sql            stop         table     version

scala> spark.
!=   ->             baseRelationToDataFrame   createDataFrame   emptyDataset   equals         getClass    isInstanceOf      newSession   range        sparkContext   stop           table      version
##   ==             catalog                   createDataset     ensuring       experimental   hashCode    listenerManager   notify       read         sql            streams        toString   wait
+    asInstanceOf   conf                      emptyDataFrame    eq             formatted      implicits   ne                notifyAll    readStream   sqlContext     synchronized   udf        →
~~~

## SparkSession Encapsulates SparkContext

Lastly, for historical context, let’s briefly understand the SparkContext’s underlying functionality.

![](/images/spark/sparkcontext.png)
SparkContext as it relates to Driver and Cluster Manager

As shown in the diagram, a SparkContext is a conduit to access all Spark functionality; only a single SparkContext exists per JVM. The Spark driver program uses it to connect to the cluster manager to communicate, submit Spark jobs and knows what resource manager (YARN, Mesos or Standalone) to communicate to. It allows you to configure Spark configuration parameters. And through SparkContext, the driver can access other contexts such as SQLContext, HiveContext, and StreamingContext to program Spark.

However, with Spark 2.0, SparkSession can access all aforementioned Spark’s functionality through a single-unified point of entry. As well as making it simpler to access DataFrame and Dataset APIs, it also subsumes the underlying contexts to manipulate data.

In summation, what I demonstrated in this blog is that all functionality previously available through SparkContext, SQLContext or HiveContext in early versions of Spark are now available via SparkSession. In essence, SparkSession is a single-unified entry point to manipulate data with Spark, minimizing number of concepts to remember or construct. Hence, if you have fewer programming constructs to juggle, you’re more likely to make fewer mistakes and your code is likely to be less cluttered.

