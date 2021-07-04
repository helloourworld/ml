---
layout: post
title: Graphx operation 1
category: Spark
catalog: yes
description: Graph Operators
tags:
    - Spark
    - Graphx
---

This page is maily from the web http://blog.csdn.net/u013045749/article/details/50483407 sth changed for 2.0.

```scala
import org.apache.spark._
import org.apache.spark.SparkContext
import org.apache.spark.graphx._
import org.apache.spark.rdd.RDD

    Logger.getLogger("org.apache.spark").setLevel(Level.ERROR)
    val conf = new SparkConf()
    conf.setAppName("DeviceCom-" + System.getenv("USER")).setMaster("local[*]")
    conf.set("spark.testing.memory", "2147480000")
    val sc = new SparkContext(conf)
    // v.csv
    // 1,AAAA,100
    // 2,BBBB,200
    // 3,CCCC,300
    val vertexLines: RDD[String] = sc.textFile("data/graphx/v.csv")
    val vertices: RDD[(VertexId, (String, Long))] = vertexLines.map(line => {
      val cols = line.split(",")
      (cols(0).toLong, (cols(1), cols(2).toLong))
    })

    // e.csv
    // 1,2,100,2014/12/1
    // 2,3,200,2014/12/2
    // 3,1,300,2014/12/3
    val format = new java.text.SimpleDateFormat("yyyy/MM/dd")
    val edgeLines: RDD[String] = sc.textFile("data/graphx/e.csv")
    val edges: RDD[Edge[((Long, java.util.Date))]] = edgeLines.map(line => {
      val cols = line.split(",")
      Edge(cols(0).toLong, cols(1).toLong, (cols(2).toLong, format.parse(cols(3))))
    })

    // 使用vertices和edges生成图
    val graph: Graph[(String, Long), (Long, java.util.Date)] = Graph(vertices, edges)

    println("\n\n~~~~~~~~~ Confirm Edges Internal of graph ")
    graph.edges.collect.foreach(println(_))
    // Edge(1,2,(100,Mon Dec 01 00:00:00 EST 2014))
    // Edge(2,3,(200,Tue Dec 02 00:00:00 EST 2014))
    // Edge(3,1,(300,Wed Dec 03 00:00:00 EST 2014))

    println("\n\n~~~~~~~~~ Confirm Vertices Internal of graph ")
    graph.vertices.collect.foreach(println(_))
    // (2,(BBBB,200))
    // (1,(AAAA,100))
    // (3,(CCCC,300))

    // 使用mapVertices修改顶点的属性，由原先的(String, Long)修改为（String的length*Long的值）
    val graph2: Graph[Long, (Long, java.util.Date)] = graph.mapVertices((vid: VertexId, attr: (String, Long)) => attr._1.length * attr._2)

    println("\n\n~~~~~~~~~ Confirm Vertices Internal of graph2 ")
    graph2.vertices.collect.foreach(println(_))
    // (2,800) BBBB的长度为4，乘以200得到800，下同
    // (1,400)
    // (3,1200)

    // 使用mapEdges将edge的属性由(100,Mon Dec 01 00:00:00 EST 2014)变为100
    val graph3: Graph[(String, Long), Long] = graph.mapEdges(edge => edge.attr._1)

    println("\n\n~~~~~~~~~ Confirm Vertices Internal of graph3 ")
    graph3.edges.collect.foreach(println(_))
    // Edge(1,2,100)
    // Edge(2,3,200)
    // Edge(3,1,300)

    println("\n\n~~~~~~~~~ Confirm triplets Internal of graph ")
    graph.triplets.collect.foreach(println(_))
    // ((1,(AAAA,100)),(2,(BBBB,200)),(100,Mon Dec 01 00:00:00 EST 2014))
    // ((2,(BBBB,200)),(3,(CCCC,300)),(200,Tue Dec 02 00:00:00 EST 2014))
    // ((3,(CCCC,300)),(1,(AAAA,100)),(300,Wed Dec 03 00:00:00 EST 2014))
    // 到这里可以观察到，上述操作对graph本身并没有影响

    // 使用mapTriplets对三元组整体进行操作，即可以利用srcAttr attr dstAttr来修改attr的信息
    val graph4: Graph[(String, Long), Long] = graph.mapTriplets(edge => edge.srcAttr._2 + edge.attr._1 + edge.dstAttr._2)

    println("\n\n~~~~~~~~~ Confirm Vertices Internal of graph4 ")
    graph4.edges.collect.foreach(println(_))
    // Edge(1,2,400) //400 = 100+200+100
    // Edge(2,3,700)
    // Edge(3,1,700)

    // 使用mapReduceTriplets来生成新的VertexRDD
    // 利用map对每一个三元组进行操作
    // 利用reduce对相同Id的顶点属性进行操作
    val newVertices: VertexRDD[Long] = graph.aggregateMessages(
      triplet => {
        // Map Function
        triplet.sendToSrc(triplet.srcAttr._2 - triplet.attr._1)
        triplet.sendToDst(triplet.dstAttr._2 + triplet.attr._1)
      },

      (a1: Long, a2: Long) => (a1 + a2)
    )

    println("\n\n~~~~~~~~~ Confirm Vertices Internal of newVertices ")
    newVertices.collect.foreach(println(_))
    // (2,300)
    // (1,400)
    // (3,500)
```