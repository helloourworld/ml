---
layout: post
title: 推荐系统中的评测指标
category: MLAdvance
catalog: yes
description: 推荐系统中的评测指标
tags:
    - Machine Learning
    - Python
---

$$ \textrm{RMSE} = \sqrt{\frac{1}{n} \sum_{i=1}^{n} (y_i - \hat{y}_i)^2} $$

```
from sklearn.metrics import mean_squared_error
RMSE = mean_squared_error(y, y_pred)**0.5
```

$$
\begin{bmatrix}1 & 4 & 3 & 2\\ 0 & 1 & 2 & 4\\ 1 & 4 & 3 & 2\\ 1 & 4 & 3 & 2 \end{bmatrix} =
\begin{bmatrix}u_{11} & u_{12} \\ u_{21} & u_{22} \\ u_{31} & u_{32}\\ u_{41} & u_{42}  \end{bmatrix} *
\begin{bmatrix}m_{11} & m_{12} & m_{13} & m_{14}\\ m_{21} & m_{22} & m_{23} & m_{24}\end{bmatrix}

$$

lines.foreachRDD(rdd => {
      rdd.foreach(x => {
        println(x)
      })
    })

```
scala> val a = sc.textFile("README.md").filter(s => s contains "root")
a: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[2] at filter at <console>:24
//toDebugString方法会打印出RDD的家族关系
//可以看到textFile方法会生成两个RDD，分别是HadoopRDD
//MapPartitionsRDD，而filter同时也会生成新的MapPartitionsRDD
scala> a.toDebugString
res0: String =
(2) MapPartitionsRDD[2] at filter at <console>:24 []
 |  README.md MapPartitionsRDD[1] at textFile at <console>:24 []
 |  README.md HadoopRDD[0] at textFile at <console>:24 []
```
```
scala> val data =Array(Array(1, 2, 3, 4, 5),Array(4,5,6))
data: Array[Array[Int]] = Array(Array(1, 2, 3, 4, 5), Array(4, 5, 6))

scala>  val rdd1=sc.parallelize(data)
rdd1: org.apache.spark.rdd.RDD[Array[Int]] = ParallelCollectionRDD[3] at parallelize at <console>:26

scala> rdd1.toDebugString
res1: String = (6) ParallelCollectionRDD[3] at parallelize at <console>:26 []

scala> val rdd2 = rdd1.flatMap(x => x.map(y => y))
rdd2: org.apache.spark.rdd.RDD[Int] = MapPartitionsRDD[4] at flatMap at <console>:28

scala> rdd2.toDebugString
res2: String =
(6) MapPartitionsRDD[4] at flatMap at <console>:28 []
 |  ParallelCollectionRDD[3] at parallelize at <console>:26 []

scala> rdd2.collect()
res3: Array[Int] = Array(1, 2, 3, 4, 5, 4, 5, 6)

scala> val rdd3 = rdd1.map(x => x.map(y => y))
rdd3: org.apache.spark.rdd.RDD[Array[Int]] = MapPartitionsRDD[5] at map at <console>:28

scala> rdd3.collect()
res4: Array[Array[Int]] = Array(Array(1, 2, 3, 4, 5), Array(4, 5, 6))
```
