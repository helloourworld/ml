---
layout: post
title: Spark-ML-0204-Hypothesis testing
category: MLAdvance
catalog: yes
description: Spark机器学习算法学习——BasicStatistics——Hypothesis testing
tags:
    - Machine Learning
    -  Spark
---
# 假设检测

&emsp;&emsp;假设检测是统计中有力的工具，它用于判断一个结果是否在统计上是显著的、这个结果是否有机会发生。`spark.mllib`目前支持皮尔森卡方检测。输入属性的类型决定是作拟合优度([goodness of fit](https://en.wikipedia.org/wiki/Goodness_of_fit))检测还是作独立性检测。
拟合优度检测需要输入数据的类型是`vector`，独立性检测需要输入数据的类型是`Matrix`。

&emsp;&emsp;`spark.mllib`也支持输入数据类型为`RDD[LabeledPoint]`，它用来通过卡方独立性检测作特征选择。`Statistics`提供方法用来作皮尔森卡方检测。下面是一个例子：

```scala
    import org.apache.spark.mllib.linalg._
    import org.apache.spark.mllib.regression.LabeledPoint
    import org.apache.spark.mllib.stat.Statistics
    import org.apache.spark.mllib.stat.test.ChiSqTestResult
    import org.apache.spark.rdd.RDD

    // a vector composed of the frequencies of events
    val vec: Vector = Vectors.dense(1,2,3,4,5)
//    val vec: Vector = Vectors.dense(0.1, 0.15, 0.2, 0.3, 0.25)

    // compute the goodness of fit. If a second vector to test against is not supplied
    // as a parameter, the test runs against a uniform distribution.
    val goodnessOfFitTestResult = Statistics.chiSqTest(vec)
    // summary of the test including the p-value, degrees of freedom, test statistic, the method
    // used, and the null hypothesis.
    println(s"$goodnessOfFitTestResult\n")

    // a contingency matrix. Create a dense matrix ((1.0, 2.0), (3.0, 4.0), (5.0, 6.0))
    val mat: Matrix = Matrices.dense(3, 2, Array(1.0, 3.0, 5.0, 2.0, 4.0, 6.0))

    // conduct Pearson's independence test on the input contingency matrix
    val independenceTestResult = Statistics.chiSqTest(mat)
    // summary of the test including the p-value, degrees of freedom
    println(s"$independenceTestResult\n")

    val obs: RDD[LabeledPoint] =
      sc.parallelize(
        Seq(
          LabeledPoint(1.0, Vectors.dense(1.0, 0.0, 3.0)),
          LabeledPoint(1.0, Vectors.dense(1.0, 2.0, 0.0)),
          LabeledPoint(-1.0, Vectors.dense(-1.0, 0.0, -0.5)
          )
        )
      ) // (feature, label) pairs.

    // The contingency table is constructed from the raw (feature, label) pairs and used to conduct
    // the independence test. Returns an array containing the ChiSquaredTestResult for every feature
    // against the label.
    val featureTestResults: Array[ChiSqTestResult] = Statistics.chiSqTest(obs)
    featureTestResults.zipWithIndex.foreach { case (k, v) =>
      println("Column " + (v + 1).toString + ":")
      println(k + "\n")
    }  // summary of the test

```
&emsp;&emsp;另外，`spark.mllib`提供了一个`Kolmogorov-Smirnov (KS)`检测的`1-sample, 2-sided`实现，用来检测概率分布的相等性。通过提供理论分布（现在仅仅支持正太分布）的名字以及它相应的参数，
或者提供一个计算累积分布(`cumulative distribution`)的函数，用户可以检测原假设或零假设(`null hypothesis`)：即样本是否来自于这个分布。用户检测正太分布，但是不提供分布参数，检测会默认该分布为标准正太分布。

&emsp;&emsp;`Statistics`提供了一个运行`1-sample, 2-sided KS`检测的方法，下面就是一个应用的例子。

```scala
    import org.apache.spark.mllib.stat.Statistics
    import org.apache.spark.rdd.RDD

    val data: RDD[Double] = sc.parallelize(Seq(0.1, 0.15, 0.2, 0.3, 0.25))  // an RDD of sample data

    // run a KS test for the sample versus a standard normal distribution
    val testResult = Statistics.kolmogorovSmirnovTest(data, "norm", 0, 1)
    // summary of the test including the p-value, test statistic, and null hypothesis if our p-value
    // indicates significance, we can reject the null hypothesis.
    println(testResult)
    println()

    // perform a KS test using a cumulative distribution function of our making
    val myCDF = Map(0.1 -> 0.2, 0.15 -> 0.6, 0.2 -> 0.05, 0.3 -> 0.05, 0.25 -> 0.1)
    val testResult2 = Statistics.kolmogorovSmirnovTest(data, myCDF)
    println(testResult2)
```

## 流式显著性检测

&emsp;&emsp;显著性检验即用于实验处理组与对照组或两种不同处理的效应之间是否有差异，以及这种差异是否显著的方法。

&emsp;&emsp;常把一个要检验的假设记作`H0`,称为原假设（或零假设） (`null hypothesis`) ，与`H0`对立的假设记作`H1`，称为备择假设(`alternative hypothesis`) 。

- 在原假设为真时，决定放弃原假设，称为第一类错误，其出现的概率通常记作`alpha`

- 在原假设不真时，决定接受原假设，称为第二类错误，其出现的概率通常记作`beta`

&emsp;&emsp;通常只限定犯第一类错误的最大概率`alpha`， 不考虑犯第二类错误的概率`beta`。这样的假设检验又称为显著性检验，概率`alpha`称为显著性水平。

&emsp;&emsp;`MLlib`提供一些检测的在线实现，用于支持诸如`A/B`测试的场景。这些检测可能执行在`Spark Streaming`的`DStream[(Boolean,Double)]`上，元组的第一个元素表示控制组(`control group (false)`)或者处理组(` treatment group (true)`),
第二个元素表示观察者的值。

&emsp;&emsp;流式显著性检测支持下面的参数：

- `peacePeriod`：来自流中忽略的初始数据点的数量，用于减少`novelty effects`；

- `windowSize`：执行假设检测的以往批次的数量。如果设置为0，将对之前所有的批次数据作累积处理。

&emsp;&emsp;`StreamingTest`支持流式假设检测。下面是一个应用的例子。

```scala
val data = ssc.textFileStream(dataDir).map(line => line.split(",") match {
  case Array(label, value) => BinarySample(label.toBoolean, value.toDouble)
})
val streamingTest = new StreamingTest()
  .setPeacePeriod(0)
  .setWindowSize(0)
  .setTestMethod("welch")
val out = streamingTest.registerStream(data)
out.print()
```

# 参考文献

【1】[显著性检验](http://wiki.mbalib.com/wiki/Significance_Testing)\\
[皮尔森独立性检测](https://en.wikipedia.org/wiki/Pearson%27s_chi-squared_test)
