---
layout: post
title: 新三板聚类项目
category: MLAdvance
catalog: yes
description: 新三板公司聚类项目说明
tags:
    - Scala
    - clustering
---

## 目录

* 1 聚类类别确定demo
* 2 聚类demo
* 3 spark MySQL 读取数据
* 4 其它

## 1 聚类类别确定demo

### 1.1 K-means 聚类算法原理

* 聚类分析是一个无监督学习 (Unsupervised Learning) 过程, 一般是用来对数据对象按照其特征属性进行分组，经常被应用在客户分群，欺诈检测，图像分析等领域。K-means 应该是最有名并且最经常使用的聚类算法了，其原理比较容易理解，并且聚类效果良好，有着广泛的使用。
* 和诸多机器学习算法一样，K-means 算法也是一个迭代式的算法，其主要步骤如下:

> * 第一步，选择 K 个点作为初始聚类中心。
> * 第二步，计算其余所有点到聚类中心的距离，并把每个点划分到离它最近的聚类中心所在的聚类中去。在这里，衡量距离一般有多个函数可以选择，最常用的是欧几里得距离 (Euclidean Distance), 也叫欧式距离。公式如下：
> * $$ D(C, X) = \sqrt{\sum_{i=1}^{n}(c_i - x_i)^2} $$
> * 其中 C 代表中心点，X 代表任意一个非中心点。
> * 第三步，重新计算每个聚类中所有点的平均值，并将其作为新的聚类中心点。
> * 最后，重复 (二)，(三) 步的过程，直至聚类中心不再发生改变，或者算法达到预定的迭代次数，又或聚类中心的改变小于预先设定的阀值。

* 在实际应用中，K-means 算法有两个不得不面对并且克服的问题。
* 1聚类个数 K 的选择。K 的选择是一个比较有学问和讲究的步骤，我们会在本部分描述如何使用 Spark 提供的工具选择 K。
* 2 初始聚类中心点的选择。选择不同的聚类中心可能导致聚类结果的差异。

Spark MLlib K-means 算法的实现在初始聚类点的选择上，借鉴了一个叫 K-means\|\|的类 K-means++ 实现。K-means++ 算法在初始点选择上遵循一个基本原则: 初始聚类中心点相互之间的距离应该尽可能的远。基本步骤如下:

> * 第一步，从数据集 X 中随机选择一个点作为第一个初始点。
> * 第二步，计算数据集中所有点与最新选择的中心点的距离 D(x)。
> * 第三步，选择下一个中心点，使得
> * $$ P(x) = \frac{D(x)^2}{\sum_{x \in X D(x)^2}} $$
> * 最大。
> * 第四步，重复 (二),(三) 步过程，直到 K 个初始点选择完成。

### 1.2 如何选择 K

本部分数据来自UCI Machine Learning Repository 的 [Wholesale customer Data Set](http://archive.ics.uci.edu/ml/datasets/Wholesale+customers)。UCI 是一个关于机器学习测试数据的下载中心站点，里面包含了适用于做聚类，分群，回归等各种机器学习问题的数据集。

Wholesale customer Data Set 是引用某批发经销商的客户在各种类别产品上的年消费数。为了方便处理，本文把原始的 CSV 格式转化成了两个文本文件，分别是训练用数据和测试用数据。

数据预览：

| Channel |Region |Fresh   |Milk   | Grocery|Frozen|Detergents_Paper |Delicassen|
| 2            |   3        |  12669| 9656|   7561   | 214     |2674                     |1338|
| 2            |   3        |  7057  | 9810|    9568  | 1762   | 3293                    | 1776|
| 2            |   3        |  6353  | 8808|    7684  | 2405   | 3516                    |  7844|

前面提到 K 的选择是 K-means 算法的关键，Spark MLlib 在 KMeansModel 类里提供了 computeCost 方法，该方法通过计算所有数据点到其最近的中心点的平方和来评估聚类的效果。一般来说，同样的迭代次数和算法跑的次数，这个值越小代表聚类的效果越好。但是在实际情况下，我们还要考虑到聚类结果的可解释性，不能一味的选择使 computeCost 结果值最小的那个 K。

~~~scala

import org.apache.spark.mllib.clustering.{KMeans, KMeansModel}
import org.apache.spark.mllib.linalg.Vectors
import org.apache.spark.{SparkConf, SparkContext}

object KMeansClusteringKChoiced {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("KMeansClusteringKChoiced").setMaster("local[2]")
    val sc = new SparkContext(conf)

    /**
      * Channel Region Fresh   Milk Grocery Frozen Detergents_Paper Delicassen
      * 2       3    12669 9656 7561   214     2674                1338
      * 2       3    7057 9810 9568    1762    3293                1776
      * 2       3    6353 8808 7684    2405    3516                7844
      */

    val rawTrainingData = sc.textFile("data/mllib/UCI/Wholesale_customers_data.csv")
    val rawData =
      rawTrainingData.filter(!isColumnNameLine(_)).map(line => {

        Vectors.dense(line.split(",").map(_.trim).filter(!"".equals(_)).map(_.toDouble))
      }).randomSplit(Array(0.8,0.2))

    // Cluster the data into two classes using KMeans
    val parsedTrainingData = rawData(0).cache()
    println("rawTrainingData is: " + rawTrainingData.count() + " ******* and parsedTrainingData is: " + parsedTrainingData.count())
    var clusterIndex: Int = 0
    val ks: Array[Int] = Array(3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20)
    ks.foreach(cluster => {
      val model: KMeansModel = KMeans.train(parsedTrainingData, cluster, 30, 1)
      val ssd = model.computeCost(parsedTrainingData)
      println("sum of squared distances of points to their nearest center when k=" + cluster + " -> " + ssd)
    })
  }
  private def
  isColumnNameLine(line:String):Boolean = {
    if (line != null &&
      line.contains("Channel")) true
    else false
  }
}
~~~

结果如下：

~~~shell
rawTrainingData is: 441 ******* and parsedTrainingData is: 356
sum of squared distances of points to their nearest center when k=3 -> 7.140043596954352E10
sum of squared distances of points to their nearest center when k=4 -> 5.828120398877622E10
sum of squared distances of points to their nearest center when k=5 -> 4.877187386940861E10
sum of squared distances of points to their nearest center when k=6 -> 4.159989838378546E10
sum of squared distances of points to their nearest center when k=7 -> 3.668188571522888E10
sum of squared distances of points to their nearest center when k=8 -> **3.583448066234746E10**
sum of squared distances of points to their nearest center when k=9 -> 2.8447515194141922E10
sum of squared distances of points to their nearest center when k=10 -> 2.6456968510064972E10
sum of squared distances of points to their nearest center when k=11 -> 2.4973296946980904E10
sum of squared distances of points to their nearest center when k=12 -> 2.284145771589616E10
sum of squared distances of points to their nearest center when k=13 -> 2.214691923763228E10
sum of squared distances of points to their nearest center when k=14 -> 2.087706648769969E10
sum of squared distances of points to their nearest center when k=15 -> 1.948901455097017E10
sum of squared distances of points to their nearest center when k=16 -> 1.9436597320674072E10
sum of squared distances of points to their nearest center when k=17 -> 1.56625179067911E10
sum of squared distances of points to their nearest center when k=18 -> 1.579405375095717E10
sum of squared distances of points to their nearest center when k=19 -> 1.3642911747596188E10
sum of squared distances of points to their nearest center when k=20 -> 1.3876552141098682E10
~~~

从上图的运行结果可以看到，当 K=9 时，cost 值有波动，但是后面又逐渐减小了，所以我们选择 8 这个临界点作为 K 的个数。当然可以多跑几次，找一个稳定的 K 值。理论上 K 的值越大，聚类的 cost 越小，极限情况下，每个点都是一个聚类，这时候 cost 是 0，但是显然这不是一个具有实际意义的聚类结果。

## 2 聚类demo

**案例分析和编码实现**

接上述K类别选择，我们将根据目标客户的消费数据，将每一列视为一个特征指标，对数据集进行聚类分析。代码实现如下。

~~~scala
import org.apache.spark.{SparkContext, SparkConf}
import org.apache.spark.mllib.clustering.{KMeans, KMeansModel}
import org.apache.spark.mllib.linalg.Vectors

object KMeansClustering {
  def main(args: Array[String]): Unit = {
    if (args.length < 4) {
      println("Usage:KMeansClustering rawDataFilePath numClusters numIterations runTimes")
      sys.exit(1)
    }
    val conf = new SparkConf().setAppName("KMeansClustering").setMaster("local[2]")
    val sc = new SparkContext(conf)

    /**
      * Channel Region Fresh   Milk Grocery Frozen Detergents_Paper Delicassen
      * 2       3    12669 9656 7561   214     2674                1338
      * 2       3    7057 9810 9568    1762    3293                1776
      * 2       3    6353 8808 7684    2405    3516                7844
      */

    val rawTrainingData = sc.textFile(args(0))
    val rawData =
      rawTrainingData.filter(!isColumnNameLine(_)).map(line => {

        Vectors.dense(line.split(",").map(_.trim).filter(!"".equals(_)).map(_.toDouble))
      }).randomSplit(Array(0.7, 0.3))

    // Cluster the data into two classes using KMeans
    val parsedTrainingData = rawData(0).cache()

    val numClusters = args(1).toInt
    val numIterations = args(2).toInt
    val runTimes = args(3).toInt
    var clusterIndex: Int = 0
    val clusters: KMeansModel = KMeans.train(parsedTrainingData, numClusters, numIterations, runTimes)
    println("Cluster Number:" + clusters.clusterCenters.length)
    println("Cluster Centers Information Overview:")
    clusters.clusterCenters.foreach(
      x => {
        println("Center Point of Cluster " + clusterIndex + ":" + x)
        clusterIndex += 1
      })

    //begin to check which cluster each test data belongs to based on the clustering result
    val parsedTestData = rawData(1)
    parsedTestData.collect().foreach(testDataLine => {
      val predictedClusterIndex: Int = clusters.predict(testDataLine)
      println("The data " + testDataLine.toString + " belongs to cluster " + predictedClusterIndex)
    })

    println("Spark MLlib K-means clustering test finished.")
  }

  private def
  isColumnNameLine(line: String): Boolean = {
    if (line != null &&
      line.contains("Channel")) true
    else false
  }
}
~~~

* 该示例程序接受五个入参，分别是
* 数据集文件路径
* 聚类的个数
* K-means 算法的迭代次数
* K-means 算法 run 的次数

** K-means 聚类示例程序运行结果**

~~~shell
Cluster Number:8
Cluster Centers Information Overview:
Center Point of Cluster 0:[2.0,2.5,15958.833333333332,35388.166666666664,56218.666666666664,1695.0,28223.5,1895.3333333333333]
Center Point of Cluster 1:[1.1818181818181819,2.5454545454545454,32552.666666666668,5293.030303030303,5921.909090909091,4403.242424242424,1080.7878787878788,2077.090909090909]
Center Point of Cluster 2:[1.2589285714285714,2.526785714285714,4186.125,4168.785714285714,4935.9375,1649.5,1678.6517857142856,1189.3392857142856]
Center Point of Cluster 3:[1.9387755102040816,2.5510204081632653,5369.69387755102,12311.571428571428,19326.61224489796,1578.6326530612243,8516.877551020407,2398.6938775510203]
Center Point of Cluster 4:[1.0,2.6,73900.8,9115.6,9777.800000000001,13285.0,1737.4,3538.6000000000004]
Center Point of Cluster 5:[1.0,3.0,36847.0,43950.0,20170.0,36534.0,239.0,47943.0]
Center Point of Cluster 6:[1.1891891891891893,2.5135135135135136,15212.662162162163,2978.1081081081084,4149.824324324324,2424.4594594594596,1025.6891891891892,1135.9864864864865]
Center Point of Cluster 7:[1.0476190476190474,2.380952380952381,8548.238095238095,2158.333333333333,2812.333333333333,9408.333333333332,443.0,934.1428571428571]
The data [2.0,3.0,22615.0,5410.0,7198.0,3915.0,1777.0,5185.0] belongs to cluster 6
The data [2.0,3.0,3366.0,5403.0,12974.0,4400.0,5977.0,1744.0] belongs to cluster 2
...
The data [1.0,3.0,16731.0,3922.0,7994.0,688.0,2371.0,838.0] belongs to cluster 6
The data [1.0,3.0,29703.0,12051.0,16027.0,13135.0,182.0,2204.0] belongs to cluster 1
The data [1.0,3.0,39228.0,1431.0,764.0,4510.0,93.0,2346.0] belongs to cluster 1
The data [1.0,3.0,10290.0,1981.0,2232.0,1038.0,168.0,2125.0] belongs to cluster 6
The data [1.0,3.0,2787.0,1698.0,2510.0,65.0,477.0,52.0] belongs to cluster 2
Spark MLlib K-means clustering test finished.
~~~

## 3 spark MySQL 读取数据

上述例子已经完整地描述了如何用K-means进行聚类分析。测试数据集很小。通常情况下我们会从数据库中直接读取数据。以下为使用spark读取MySQL数据示例。

~~~
import org.apache.spark.sql.SparkSession
import org.apache.spark.{SparkConf, SparkContext}

object KMeansDF {
  def main(args: Array[String]): Unit = {

    val conf = new SparkConf().setAppName("KMeansDF").setMaster("local[2]")
    val sc = new SparkContext(conf)
    val spark = SparkSession
      .builder()
      .appName("KMeansDF")
      .config("spark.sql.warehouse.dir", "file:///d:/root/")
      .getOrCreate()
    val sourceUrl = "jdbc:mysql://192.168.71.128:3306/test?user=hive&password=****"
    val df = spark
      .read
      .format("jdbc")
      .option("url", sourceUrl)
      .option("dbtable", "wholesales")
      .load()
    //    val rawTrainingData = sc.textFile("data/mllib/UCI/Wholesale_customers_data.csv")

    val rawData =
      df.randomSplit(Array(0.7, 0.3))

    val parsedTrainingData = rawData(0)
    val parsedTestData = rawData(1)
    println("split to: " + rawData.size)
    println("rawDataSize: " + df.count())
    println("parsedTrainingData " + parsedTrainingData.count())
    println("parsedTestData " + parsedTestData.count())
    parsedTrainingData.take(10).foreach(println)

  }
}
~~~

## 4 其它
