---
layout: post
title: Graphx operation 2[转]
category: Spark
catalog: yes
description: Grapregel 与 spark graphX 的 pregel api 
tags:
    - Spark
    - Graphx
---

This page is maily from the web http://blog.csdn.net/u013468917/article/details/51199808 sth changed for 2.0.

## 简介

在Hadoop兴起之后，google又发布了三篇研究论文，分别阐述了了Caffeine、Pregel、Dremel三种技术，这三种技术也被成为google的新“三驾马车”，其中的Pregel是google提出的用于大规模分布式图计算框架。主要用于图遍历（BFS）、最短路径（SSSP）、PageRank计算等等计算。
在Pregel计算模式中，输入是一个有向图，该有向图的每一个顶点都有一个相应的独一无二的顶点id (vertex identifier)。每一个顶点都有一些属性，这些属性可以被修改，其初始值由用户定义。每一条有向边都和其源顶点关联，并且也拥有一些用户定义的属性和值，并同时还记录了其目的顶点的ID。 
一个典型的Pregel计算过程如下：读取输入，初始化该图，当图被初始化好后，运行一系列的supersteps，每一次superstep都在全局的角度上独立运行，直到整个计算结束，输出结果。
在pregel中顶点有两种状态：活跃状态（active）和不活跃状态（halt）。如果某一个顶点接收到了消息并且需要执行计算那么它就会将自己设置为活跃状态。如果没有接收到消息或者接收到消息，但是发现自己不需要进行计算，那么就会将自己设置为不活跃状态。这种机制的描述如下图：

![](/images/spark/graphx/pregel.png)

## 计算过程

Pregel中的计算分为一个个“superstep”，这些”superstep”中执行流程如下：
* 1、 首先输入图数据，并进行初始化。
* 2、 将每个节点均设置为活跃状态。每个节点根据预先定义好的sendmessage函数，以及方向（边的正向、反向或者双向）向周围的节点发送信息。
* 3、 每个节点接收信息如果发现需要计算则根据预先定义好的计算函数对接收到的信息进行处理，这个过程可能会更新自己的信息。如果接收到消息但是不需要计算则将自己状态设置为不活跃。
* 4、 每个活跃节点按照sendmessage函数向周围节点发送消息。
* 5、 下一个superstep开始，像步骤3一样继续计算，直到所有节点都变成不活跃状态，整个计算过程结束。
下面以一个具体例子来说明这个过程：假设一个图中有4个节点，从左到右依次为第1/2/3/4个节点。圈中的数字为节点的属性值，实线代表节点之间的边，虚线是不同超步之间的信息发送，带阴影的圈是不活跃的节点。我们的目的是让图中所有节点的属性值都变成最大的那个属性值。

![](/images/spark/graphx/pregel2.png)

* superstep 0：首先所有节点设置为活跃，并且沿正向边向相邻节点发送自身的属性值。
* Superstep 1：所有节点接收到信息，节点1和节点4发现自己接受到的值比自己的大，所以更新自己的节点(这个过程可以看做是计算)，并保持活跃。节点2和3没有接收到比自己大的值，所以不计算、不更新。活跃节点继续向相邻节点发送当前自己的属性值。
* Superstep 2：节点3接受信息并计算，其它节点没接收到信息或者接收到但是不计算，所以接下来只有节点3活跃并发送消息。
* Superstep 3：节点2和4接受到消息但是不计算所以不活跃，所有节点均不活跃，所以计算结束。
在pregel计算框架中有两个核心的函数：sendmessage函数和F(Vertex)节点计算函数。

## 实验 

hdfs上的web-Google.txt文件生成图，这个文件可以在https://snap.stanford.edu/data/web-Google.html下载。

```
/**
  * Created by yulijun on 2018/2/13.
  * http://blog.csdn.net/u013468917/article/details/51199808
  */

import org.apache.log4j.{Level, Logger}
import org.apache.spark.{SparkConf, SparkContext}
import org.apache.spark.graphx.{Graph, VertexId}
import org.apache.spark.graphx.util.GraphGenerators

object SP {
  Logger.getLogger("org.apache.spark").setLevel(Level.ERROR)
  val conf = new SparkConf()
  conf.setAppName("DeviceCom-" + System.getenv("USER")).setMaster("local[*]")
  conf.set("spark.testing.memory", "2147480000")
  val sc = new SparkContext(conf)
  // A graph with edge attributes containing distances
  val graph: Graph[Long, Double] =
    GraphGenerators.logNormalGraph(sc, numVertices = 10).mapEdges(e => e.attr.toDouble)
  val sourceId: VertexId = 0 // The ultimate source
  // Initialize the graph such that all vertices except the root have distance infinity.
  val iniGraph = graph.mapVertices((id, _) =>
    if (id == sourceId) 0.0 else Double.PositiveInfinity)
  val shortestPath = iniGraph.pregel(Double.PositiveInfinity)(
    (id, dist, newDist) => math.min(dist, newDist), // Vertex Program
    triplet => { // Send Message
      if (triplet.srcAttr + triplet.attr < triplet.dstAttr) {
        Iterator((triplet.dstId, triplet.srcAttr + triplet.attr))
      } else {
        Iterator.empty
      }
    },
    (a, b) => math.min(a, b) // Merge Message
  )
  println(shortestPath.vertices.collect.mkString("\n"))
}
```