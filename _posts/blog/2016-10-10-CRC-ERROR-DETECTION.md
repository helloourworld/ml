---
layout: post
title: CRC ERROR DETECTION
category: blog
catalog: yes
description: A PAINLESS GUIDE TO CRC ERROR DETECTION ALGORITHMS
tags:
    - IT
---



我目前了解的有一下几个用处：


1、作为“通配符”，类似Java中的*。如import scala.math._

2、:_*作为一个整体，告诉编译器你希望将某个参数当作参数序列处理！例如val s = sum(1 to 5:_*)就是将1 to 5当作参数序列处理。

3、指代一个集合中的每个元素。例如我们要在一个Array a中筛出偶数，并乘以2，可以用以下办法：
a.filter(_%2==0).map(2*_)。
又如要对缓冲数组ArrayBuffer b排序，可以这样：
val bSorted = b.sorted(_
4、在元组中，可以用方法_1, _2, _3访问组员。如a._2。其中句点可以用空格替代。

5、使用模式匹配可以用来获取元组的组员，例如
val (first, second, third) = t
但如果不是所有的部件都需要，那么可以在不需要的部件位置上使用_。比如上一例中val (first, second, _) = t

6、还有一点，下划线_代表的是某一类型的默认值。
对于Int来说，它是0。
对于Double来说，它是0.0
对于引用类型，它是null。


KNIME http://www.knime.org/knime-analytics-platform

rapidminer https://rapidminer.com/products/

http://www.datamation.com/data-center/slideshows/8-open-source-big-data-mining-tools.html

http://optimalbi.com/blog/2016/02/25/looking-for-data-mining-and-analytics-tools-check-out-these-open-source-options/



C struct

## Spark常用函数讲解之键值RDD转换(http://www.cnblogs.com/MOBIN/p/5384543.html)

val kv2=sc.parallelize(List(("A",4),("A",4),("C",3),("A",4),("B",5)))

kv2.groupByKey().map{
      x=>(x._1, x._2.reduce(_+_)/x._2.count(x=>true))
}.collect

