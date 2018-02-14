---
layout: post
title: Graphx prelimery
category: Spark
catalog: yes
description: graphx-programming-guide
tags:
    - Spark
    - Graphx
---
This page is maily from http://spark.apache.org/docs/2.0.2/graphx-programming-guide.html.
Just for markable.

## [Example Property Graph](http://spark.apache.org/docs/2.0.2/graphx-programming-guide.html#example-property-graph)

Suppose we want to construct a property graph consisting of the various collaborators on the GraphX project. The vertex property might contain the username and occupation. We could annotate edges with a string describing the relationships between collaborators:

![The property graph](/images/spark/graphx/property_graph.png)

~~~Scala
import org.apache.spark._
import org.apache.spark.graphx._
// To make some of the examples work we will also need RDD
import org.apache.spark.rdd.RDD

// Log / shield
import org.apache.log4j.{Logger, Level}
Logger.getLogger("org.apache.spark").setLevel(Level.ERROR)

// Create an RDD for the vertices
val users: RDD[(VertexId, (String, String))] =
  sc.parallelize(Array((3L, ("rxin", "student")), (7L, ("jgonzal", "postdoc")),
                       (5L, ("franklin", "prof")), (2L, ("istoica", "prof"))))
// Create an RDD for edges
val relationships: RDD[Edge[String]] =
  sc.parallelize(Array(Edge(3L, 7L, "collab"),    Edge(5L, 3L, "advisor"),
                       Edge(2L, 5L, "colleague"), Edge(5L, 7L, "pi")))
// Define a default user in case there are relationship with missing user
val defaultUser = ("John Doe", "Missing")
// Build the initial Graph
val graph = Graph(users, relationships, defaultUser)
~~~

In the above example we make use of the Edge case class. Edges have a srcId and a dstId corresponding to the source and destination vertex identifiers. In addition, the Edge class has an attr member which stores the edge property.

We can deconstruct a graph into the respective vertex and edge views by using the graph.vertices and graph.edges members respectively.

~~~Scala
val graph: Graph[(String, String), String] // Constructed from above
// Count all users which are postdocs
graph.vertices.filter { case (id, (name, pos)) => pos == "postdoc" }.count
// Count all the edges where src > dst
graph.edges.filter(e => e.srcId > e.dstId).count
~~~

> Note that graph.vertices returns an VertexRDD[(String, String)] which extends RDD[(VertexID, (String, String))] and so we use the scala case expression to deconstruct the tuple. On the other hand, graph.edges returns an EdgeRDD containing Edge[String] objects. We could have also used the case class type constructor as in the following

```
graph.edges.filter { case Edge(src, dst, prop) => src > dst }.count
```

In addition to the vertex and edge views of the property graph, GraphX also exposes a triplet view. The triplet view logically joins the vertex and edge properties yielding an RDD[EdgeTriplet[VD, ED]] containing instances of the EdgeTriplet class. This join can be expressed in the following SQL expression:

```
SELECT src.id, dst.id, src.attr, e.attr, dst.attr
FROM edges AS e LEFT JOIN vertices AS src, vertices AS dst
ON e.srcId = src.Id AND e.dstId = dst.Id

create table ylj_vertex(id String, property String)

insert into ylj_vertex(id , property ) values ('3', 'Stu'), ('7', 'postdoc'), ('5', 'prof'), ('2', 'prof')

create table ylj_edge(srcid String, dstid String, property String);

insert into ylj_edge(srcid, dstid , property) values('3', '7', 'Collaborator'), ('5', '3', 'Advisor'), ('2', '5', 'Colleague'), ('5', '7', 'PI')

select src.id as srcid, dst.id as dstid, src.property as p1, dst.property as p3, e.property as p2
from ylj_edge as e
left join ylj_vertex as src
left join ylj_vertex as dst
on e.srcid = src.id and e.dstid = dst.id;
```

or graphically as:
![The triplet graph](/images/spark/graphx/triplet.png)

The EdgeTriplet class extends the Edge class by adding the srcAttr and dstAttr members which contain the source and destination properties respectively. We can use the triplet view of a graph to render a collection of strings describing relationships between users.

~~~scala
val graph: Graph[(String, String), String] // Constructed from above
// Use the triplets view to create an RDD of facts.
val facts: RDD[String] =
  graph.triplets.map(triplet =>
    triplet.srcAttr._1 + " is the " + triplet.attr + " of " + triplet.dstAttr._1)
facts.collect.foreach(println(_))

val facts = graph.triplets.map(triplet => triplet.srcId + " is the " + triplet.attr + " of " + triplet.dstId)
facts.collect.foreach(println(_))
~~~


## [Graph Operators](http://spark.apache.org/docs/2.0.2/graphx-programming-guide.html#graph-operators)

Just as RDDs have basic operations like map, filter, and reduceByKey, property graphs also have a collection of basic operators that take user defined functions and produce new graphs with transformed properties and structure. The core operators that have optimized implementations are defined in Graph and convenient operators that are expressed as a compositions of the core operators are defined in [GraphOps](http://spark.apache.org/docs/2.0.2/api/scala/index.html#org.apache.spark.graphx.GraphOps). However, thanks to Scala implicits the operators in GraphOps are automatically available as members of Graph. For example, we can compute the in-degree of each vertex (defined in GraphOps) by the following:

~~~scala
val graph: Graph[(String, String), String]
// Use the implicit GraphOps.inDegrees operator
val inDegrees: VertexRDD[Int] = graph.inDegrees
~~~

**Summary List of Operators**

~~~scala
/** Summary of the functionality in the property graph */
class Graph[VD, ED] {
  // Information about the Graph ===================================================================
  val numEdges: Long
  val numVertices: Long
  val inDegrees: VertexRDD[Int]
  val outDegrees: VertexRDD[Int]
  val degrees: VertexRDD[Int]
  // Views of the graph as collections =============================================================
  val vertices: VertexRDD[VD]
  val edges: EdgeRDD[ED]
  val triplets: RDD[EdgeTriplet[VD, ED]]
  // Functions for caching graphs ==================================================================
  def persist(newLevel: StorageLevel = StorageLevel.MEMORY_ONLY): Graph[VD, ED]
  def cache(): Graph[VD, ED]
  def unpersistVertices(blocking: Boolean = true): Graph[VD, ED]
  // Change the partitioning heuristic  ============================================================
  def partitionBy(partitionStrategy: PartitionStrategy): Graph[VD, ED]
  // Transform vertex and edge attributes ==========================================================
  def mapVertices[VD2](map: (VertexID, VD) => VD2): Graph[VD2, ED]
  def mapEdges[ED2](map: Edge[ED] => ED2): Graph[VD, ED2]
  def mapEdges[ED2](map: (PartitionID, Iterator[Edge[ED]]) => Iterator[ED2]): Graph[VD, ED2]
  def mapTriplets[ED2](map: EdgeTriplet[VD, ED] => ED2): Graph[VD, ED2]
  def mapTriplets[ED2](map: (PartitionID, Iterator[EdgeTriplet[VD, ED]]) => Iterator[ED2])
    : Graph[VD, ED2]
  // Modify the graph structure ====================================================================
  def reverse: Graph[VD, ED]
  def subgraph(
      epred: EdgeTriplet[VD,ED] => Boolean = (x => true),
      vpred: (VertexID, VD) => Boolean = ((v, d) => true))
    : Graph[VD, ED]
  def mask[VD2, ED2](other: Graph[VD2, ED2]): Graph[VD, ED]
  def groupEdges(merge: (ED, ED) => ED): Graph[VD, ED]
  // Join RDDs with the graph ======================================================================
  def joinVertices[U](table: RDD[(VertexID, U)])(mapFunc: (VertexID, VD, U) => VD): Graph[VD, ED]
  def outerJoinVertices[U, VD2](other: RDD[(VertexID, U)])
      (mapFunc: (VertexID, VD, Option[U]) => VD2)
    : Graph[VD2, ED]
  // Aggregate information about adjacent triplets =================================================
  def collectNeighborIds(edgeDirection: EdgeDirection): VertexRDD[Array[VertexID]]
  def collectNeighbors(edgeDirection: EdgeDirection): VertexRDD[Array[(VertexID, VD)]]
  def aggregateMessages[Msg: ClassTag](
      sendMsg: EdgeContext[VD, ED, Msg] => Unit,
      mergeMsg: (Msg, Msg) => Msg,
      tripletFields: TripletFields = TripletFields.All)
    : VertexRDD[A]
  // Iterative graph-parallel computation ==========================================================
  def pregel[A](initialMsg: A, maxIterations: Int, activeDirection: EdgeDirection)(
      vprog: (VertexID, VD, A) => VD,
      sendMsg: EdgeTriplet[VD, ED] => Iterator[(VertexID,A)],
      mergeMsg: (A, A) => A)
    : Graph[VD, ED]
  // Basic graph algorithms ========================================================================
  def pageRank(tol: Double, resetProb: Double = 0.15): Graph[Double, Double]
  def connectedComponents(): Graph[VertexID, ED]
  def triangleCount(): Graph[Int, ED]
  def stronglyConnectedComponents(numIter: Int): Graph[VertexID, ED]
}
~~~

### [Property Operators](http://spark.apache.org/docs/2.0.2/graphx-programming-guide.html#property-operators)

Like the RDD map operator, the property graph contains the following:

```
class Graph[VD, ED] {
  def mapVertices[VD2](map: (VertexId, VD) => VD2): Graph[VD2, ED]
  def mapEdges[ED2](map: Edge[ED] => ED2): Graph[VD, ED2]
  def mapTriplets[ED2](map: EdgeTriplet[VD, ED] => ED2): Graph[VD, ED2]
}
```

Each of these operators yields a new graph with the vertex or edge properties modified by the user defined map function.

These operators are often used to initialize the graph for a particular computation or project away unnecessary properties. For example, given a graph with the out degrees as the vertex properties (we describe how to construct such a graph later), we initialize it for PageRank:

~~~scala
// Given a graph where the vertex property is the out degree
val inputGraph: Graph[Int, String] =
  graph.outerJoinVertices(graph.outDegrees)((vid, _, degOpt) => degOpt.getOrElse(0))
// Construct a graph where each edge contains the weight
// and each vertex is the initial PageRank
val outputGraph: Graph[Double, Double] =
  inputGraph.mapTriplets(triplet => 1.0 / triplet.srcAttr).mapVertices((id, _) => 1.0)
~~~

### [Structural Operators](http://spark.apache.org/docs/2.0.2/graphx-programming-guide.html#structural-operators)

Currently GraphX supports only a simple set of commonly used structural operators and we expect to add more in the future. The following is a list of the basic structural operators.

```
class Graph[VD, ED] {
  def reverse: Graph[VD, ED]
  def subgraph(epred: EdgeTriplet[VD,ED] => Boolean,
               vpred: (VertexId, VD) => Boolean): Graph[VD, ED]
  def mask[VD2, ED2](other: Graph[VD2, ED2]): Graph[VD, ED]
  def groupEdges(merge: (ED, ED) => ED): Graph[VD,ED]
}
```

The subgraph operator takes vertex and edge predicates and returns the graph containing only the vertices that satisfy the vertex predicate (evaluate to true) and edges that satisfy the edge predicate and connect vertices that satisfy the vertex predicate. The subgraph operator can be used in number of situations to restrict the graph to the vertices and edges of interest or eliminate broken links. For example in the following code we remove broken links:

```
// Create an RDD for the vertices
val users: RDD[(VertexId, (String, String))] =
  sc.parallelize(Array((3L, ("rxin", "student")), (7L, ("jgonzal", "postdoc")),
                       (5L, ("franklin", "prof")), (2L, ("istoica", "prof")),
                       (4L, ("peter", "student"))))
// Create an RDD for edges
val relationships: RDD[Edge[String]] =
  sc.parallelize(Array(Edge(3L, 7L, "collab"),    Edge(5L, 3L, "advisor"),
                       Edge(2L, 5L, "colleague"), Edge(5L, 7L, "pi"),
                       Edge(4L, 0L, "student"),   Edge(5L, 0L, "colleague")))
// Define a default user in case there are relationship with missing user
val defaultUser = ("John Doe", "Missing")
// Build the initial Graph
val graph = Graph(users, relationships, defaultUser)
// Notice that there is a user 0 (for which we have no information) connected to users
// 4 (peter) and 5 (franklin).
graph.triplets.map(
    triplet => triplet.srcAttr._1 + " is the " + triplet.attr + " of " + triplet.dstAttr._1
  ).collect.foreach(println(_))
// Remove missing vertices as well as the edges to connected to them
val validGraph = graph.subgraph(vpred = (id, attr) => attr._2 != "Missing")
// The valid subgraph will disconnect users 4 and 5 by removing user 0
validGraph.vertices.collect.foreach(println(_))
validGraph.triplets.map(
    triplet => triplet.srcAttr._1 + " is the " + triplet.attr + " of " + triplet.dstAttr._1
  ).collect.foreach(println(_))
```

The mask operator constructs a subgraph by returning a graph that contains the vertices and edges that are also found in the input graph. This can be used in conjunction with the subgraph operator to restrict a graph based on the properties in another related graph. For example, we might run connected components using the graph with missing vertices and then restrict the answer to the valid subgraph.

 ```
// Run Connected Components
val ccGraph = graph.connectedComponents() // No longer contains missing field
// Remove missing vertices as well as the edges to connected to them
val validGraph = graph.subgraph(vpred = (id, attr) => attr._2 != "Missing")
// Restrict the answer to the valid subgraph
val validCCGraph = ccGraph.mask(validGraph)
```
The groupEdges operator merges parallel edges (i.e., duplicate edges between pairs of vertices) in the multigraph. In many numerical applications, parallel edges can be added (their weights combined) into a single edge thereby **reducing the size of the graph.**

### [Join Operators](http://spark.apache.org/docs/2.0.2/graphx-programming-guide.html#join-operators)

In many cases it is necessary to join data from external collections (RDDs) with graphs. For example, we might have extra user properties that we want to merge with an existing graph or we might want to pull vertex properties from one graph into another. These tasks can be accomplished using the join operators. Below we list the key join operators:

```
class Graph[VD, ED] {
  def joinVertices[U](table: RDD[(VertexId, U)])(map: (VertexId, VD, U) => VD)
    : Graph[VD, ED]
  def outerJoinVertices[U, VD2](table: RDD[(VertexId, U)])(map: (VertexId, VD, Option[U]) => VD2)
    : Graph[VD2, ED]
}
```
### [Neighborhood Aggregation](http://spark.apache.org/docs/2.0.2/graphx-programming-guide.html#neighborhood-aggregation)

A key step in many graph analytics tasks is aggregating information about the neighborhood of each vertex. For example, we might want to know the number of followers each user has or the average age of the the followers of each user. 

#### [Aggregate Messages (aggregateMessages)](http://spark.apache.org/docs/2.0.2/graphx-programming-guide.html#aggregate-messages-aggregatemessages)

The core aggregation operation in GraphX is aggregateMessages. This operator applies a user defined sendMsg function to each edge triplet in the graph and then uses the mergeMsg function to aggregate those messages at their destination vertex.

~~~scala
class Graph[VD, ED] {
  def aggregateMessages[Msg: ClassTag](
      sendMsg: EdgeContext[VD, ED, Msg] => Unit,
      mergeMsg: (Msg, Msg) => Msg,
      tripletFields: TripletFields = TripletFields.All)
    : VertexRDD[Msg]
}
~~~

用户定义的sendMsg函数使用一个[EdgeContext](http://spark.apache.org/docs/2.0.2/api/scala/index.html#org.apache.spark.graphx.EdgeContext)通过暴露源和目标属性同边属性及函数（[sendToSrc](http://spark.apache.org/docs/2.0.2/api/scala/index.html#org.apache.spark.graphx.EdgeContext@sendToSrc(msg:A):Unit), and [sendToDst](http://spark.apache.org/docs/2.0.2/api/scala/index.html#org.apache.spark.graphx.EdgeContext@sendToDst(msg:A):Unit)）一起来发送短消息到源和目标属性。**SendMsg如同MR中的map**。

用户定义的mergeMsg函数需要两个消息达到相同的顶点，然后产生一个单一的消息。**mergeMsg如同MR中的reduce**。

aggregateMessages算子返回一个VertexRDD[Msg]去到每个顶点。接收不到消息的顶点不包含在返回的VertexRDD中。


此外，aggregateMessages带有一个可选的tripletsfields,来表明哪些数据是在edgeContext访问（例如，源顶点属性而不是目标顶点属性）。

The possible options for the tripletsFields are defined in [TripletFields](http://spark.apache.org/docs/2.0.2/api/java/org/apache/spark/graphx/TripletFields.html) and the default value is [TripletFields.All](http://spark.apache.org/docs/2.0.2/api/java/org/apache/spark/graphx/TripletFields.html#All) which indicates that the user defined sendMsg function may access any of the fields in the EdgeContext. 

The tripletFields argument can be used to notify GraphX that only part of the EdgeContext will be needed allowing GraphX to select an optimized join strategy. For example if we are computing the average age of the followers of each user we would only require the source field and so we would use TripletFields.Src to indicate that we only require the source field


In the following example we use the aggregateMessages operator to compute the average age of the more senior followers of each user.

~~~scala
import org.apache.spark.graphx.{Graph, VertexRDD}
import org.apache.spark.graphx.util.GraphGenerators

// Create a graph with "age" as the vertex property.
// Here we use a random graph for simplicity.
val graph: Graph[Double, Int] =
  GraphGenerators.logNormalGraph(sc, numVertices = 100).mapVertices( (id, _) => id.toDouble )
// Compute the number of older followers and their total age
val olderFollowers: VertexRDD[(Int, Double)] = graph.aggregateMessages[(Int, Double)](
  triplet => { // Map Function
    if (triplet.srcAttr > triplet.dstAttr) {
      // Send message to destination vertex containing counter and age
      triplet.sendToDst(1, triplet.srcAttr)
    }
  },
  // Add counter and age
  (a, b) => (a._1 + b._1, a._2 + b._2) // Reduce Function
)
// Divide total age by number of older followers to get average age of older followers
val avgAgeOfOlderFollowers: VertexRDD[Double] =
  olderFollowers.mapValues( (id, value) =>
    value match { case (count, totalAge) => totalAge / count } )
// Display the results
avgAgeOfOlderFollowers.collect.foreach(println(_))
~~~

GraphGenerators.logNormalGraph随机图生成方法源码：默认出度为4，标准偏差为1.3，并行生成numVertices，partition默认为sc的默认partition。然后Vertex的属性值生成调用了sampleLogNormal方法生成，边的生成调用了generateRandomEdges方法，总边数为每个顶点与其出度的乘积之和，默认生成的边为：Edge[Int](src, rand.nextInt(maxVertexId), 1)，也就是说目的顶点随机，可能重复也可能指向自己。 如下：

```
def logNormalGraph(  
     sc: SparkContext, numVertices: Int, numEParts: Int = 0, mu: Double = 4.0,  
     sigma: Double = 1.3, seed: Long = -1): Graph[Long, Int] = {  
  
   val evalNumEParts = if (numEParts == 0) sc.defaultParallelism else numEParts  
  
   // Enable deterministic seeding  
   val seedRand = if (seed == -1) new Random() else new Random(seed)  
   val seed1 = seedRand.nextInt()  
   val seed2 = seedRand.nextInt()  
  
   val vertices: RDD[(VertexId, Long)] = sc.parallelize(0 until numVertices, evalNumEParts).map {  
     src => (src, sampleLogNormal(mu, sigma, numVertices, seed = (seed1 ^ src)))  
   }  
  
   val edges = vertices.flatMap { case (src, degree) =>  
     generateRandomEdges(src.toInt, degree.toInt, numVertices, seed = (seed2 ^ src))  
   }  
  
   Graph(vertices, edges, 0)  
 }  
```

 ~~~scala
 val graph: Graph[Int, Float] = ...
def msgFun(triplet: EdgeContext[Int, Float, String]) {
  triplet.sendToDst("Hi")
}
def reduceFun(a: String, b: String): String = a + " " + b
val result = graph.aggregateMessages[String](msgFun, reduceFun)
~~~


#### [Pregel API](http://spark.apache.org/docs/2.0.2/graphx-programming-guide.html#pregel-api)

Graphs are inherently recursive data structures as properties of veritices depend on properties of their neighbors which in turn depend on properties of their neighbors.图本质上是递归的数据结构，因为顶点的属性取决于它们的邻居的属性，而这些属性又取决于它们邻居的属性。As a consequence[由此]many important graph algorithms iteratively recompute the properties of each vertex until a fixed-point condition is reached.A range of graph-parallel abstractions have been proposed to express these iterative algorithms. GraphX exposes a variant of the Pregel API.

At a high level the Pregel operator in GraphX is **a bulk-synchronous「批量同步」 parallel messaging abstraction** constrained to the topology of the graph. The Pregel operator executes in a series of **super steps** in which vertices receive the sum of their inbound messages from the previous super step, compute a new value for the vertex property, and then send messages to neighboring vertices in the next super step. Unlike Pregel, messages are computed in parallel as a function of the edge triplet and the message computation has access to both the source and destination vertex attributes. Vertices that do not receive a message are skipped within a super step. The Pregel operators terminates iteration and returns the final graph when there are no messages remaining.

> **Note**, unlike more standard Pregel implementations, vertices in GraphX can *only send messages to neighboring vertices and the message construction is done in parallel using a user defined messaging function*. These constraints allow additional optimization within GraphX.

The following is the type signature of the Pregel operator as well as a sketch of its implementation (note calls to graph.cache have been removed):

~~~scala

class GraphOps[VD, ED] {
  def pregel[A]
      (initialMsg: A,
       maxIter: Int = Int.MaxValue,
       activeDir: EdgeDirection = EdgeDirection.Out)
      (vprog: (VertexId, VD, A) => VD,
       sendMsg: EdgeTriplet[VD, ED] => Iterator[(VertexId, A)],
       mergeMsg: (A, A) => A)
    : Graph[VD, ED] = {
    // Receive the initial message at each vertex
    var g = mapVertices( (vid, vdata) => vprog(vid, vdata, initialMsg) ).cache()
    // compute the messages
    var messages = g.mapReduceTriplets(sendMsg, mergeMsg)
    var activeMessages = messages.count()
    // Loop until no messages remain or maxIterations is achieved
    var i = 0
    while (activeMessages > 0 && i < maxIterations) {
      // Receive the messages and update the vertices.
      g = g.joinVertices(messages)(vprog).cache()
      val oldMessages = messages
      // Send new messages, skipping edges where neither side received a message. We must cache
      // messages so it can be materialized on the next line, allowing us to uncache the previous
      // iteration.
      messages = g.mapReduceTriplets(
        sendMsg, mergeMsg, Some((oldMessages, activeDirection))).cache()
      activeMessages = messages.count()
      i += 1
    }
    g
  }
}
~~~

各个参数的意义详细解释如下：

initialMsg: 初始化消息，这个初始消息会被用来初始化图中的每个节点的属性，在pregel进行调用时，会首先在图上使用mapVertices来根据initialMsg的值更新每个节点的值，至于如何更新，则由vprog参数而定，vprog函数就接收了initialMsg消息做为参数来更新对应节点的值

maxIterations: 最大迭代次数

activeDirection: 表示边的活跃方向，什么是活跃方向呢，首先要解释一下活跃消息与活跃顶点的概念，活跃节点是指在某一轮迭代中，pregel会以sendMsg和mergeMsg为参数来调用graph的aggregateMessage方法后收到消息的节点，活跃消息就是这轮迭代中所有被收成功收到的消息。这样一来，有的边的src节点是活跃节点，有的dst节点是活跃节点，而有的边两端节点都是活跃节点。如果activeDirection参数指定为“EdgeDirection.Out”,则在下一轮迭代时，只有接收消息的出边(src—>dst)才会执行sendMsg函数，也就是说，sendMsg回调函数会过滤掉”dst—>src”的edgeTriplet上下文参数

vprog: 节点变换函数，在初始时，以及每轮迭代后，pregel会根据上一轮使用的msg和这里的vprod函数在图上调用joinVertices方法变化每个收到消息的节点，注意这个函数除初始时外，都是仅在接收到消息的节点上运行，这一点可以从源码中看到，源码中用的是joinVertices(message)(vprog)，因此，没有收到消息的节点在join之后就滤掉了

sendMsg: 消息发送函数，该函数的运行参数是一个代表边的上下文，pregel在调用aggregateMessages时，会将EdgeContext转换成EdgeTriplet对象(ctx.toEdgeTriplet)来使用，用户需要通过Iterator[(VertexId,A)]指定发送哪些消息，发给那些节点，发送的内容是什么，因为在一条边上可以发送多个消息，如sendToDst，如sendToSrc，所以这里是个Iterator，每一个元素是一个tuple，其中的vertexId表示要接收此消息的节点的id，它只能是该边上的srcId或dstId，而A就是要发送的内容，因此如果是需要由src发送一条消息A给dst，则有：Iterator((dstId,A))，如果什么消息也不发送，则可以返回一个空的Iterator：Iterator.empty

mergeMsg: 邻居节点收到多条消息时的合并逻辑，注意它区别于vprog函数，mergeMsg仅能合并消息内容，但合并后并不会更新到节点中去，而vprog函数可以根据收到的消息(就是mergeMsg产生的结果)更新节点属性。

Notice that Pregel takes two argument lists (i.e., graph.pregel(list1)(list2)). The first argument list contains configuration parameters including the initial message, the maximum number of iterations, and the edge direction in which to send messages (by default along out edges). The second argument list contains the user defined functions for receiving messages **(the vertex program vprog)**, **computing messages (sendMsg)**, and **combining messages mergeMsg**.

We can use the Pregel operator to express computation such as single source shortest path in the following example.

~~~scala
import org.apache.spark.graphx.{Graph, VertexId}
import org.apache.spark.graphx.util.GraphGenerators

// A graph with edge attributes containing distances
val graph: Graph[Long, Double] =
  GraphGenerators.logNormalGraph(sc, numVertices = 100).mapEdges(e => e.attr.toDouble)
val sourceId: VertexId = 42 // The ultimate source
// Initialize the graph such that all vertices except the root have distance infinity.
val initialGraph = graph.mapVertices((id, _) =>
    if (id == sourceId) 0.0 else Double.PositiveInfinity)
val sssp = initialGraph.pregel(Double.PositiveInfinity)(
  (id, dist, newDist) => math.min(dist, newDist), // Vertex Program
  triplet => {  // Send Message
    if (triplet.srcAttr + triplet.attr < triplet.dstAttr) {
      Iterator((triplet.dstId, triplet.srcAttr + triplet.attr))
    } else {
      Iterator.empty
    }
  },
  (a, b) => math.min(a, b) // Merge Message
)
println(sssp.vertices.collect.mkString("\n"))

~~~

## Graph Algorithms

GraphX includes a set of graph algorithms to simplify analytics tasks. The algorithms are contained in the org.apache.spark.graphx.lib package and can be accessed directly as methods on Graph via GraphOps. This section describes the algorithms and how they are used.

### [PageRank](http://spark.apache.org/docs/2.0.2/graphx-programming-guide.html#pagerank)


~~~data
# followers
2 1
4 1
1 2
6 3
7 3
7 6
6 7
3 7

# users
1,BarackObama,Barack Obama, USA
2,ladygaga,Goddess of Love, Gaga
3,jeresig,John Resig, JQuery
4,justinbieber,Justin Bieber, Bieber
6,matei_zaharia,Matei Zaharia, Databrics
7,odersky,Martin Odersky, Scala
8,anonsys
~~~

~~~scala
import org.apache.spark.graphx.GraphLoader

// Load the edges as a graph
val graph = GraphLoader.edgeListFile(sc, "data/graphx/followers.txt")
// Run PageRank
val ranks = graph.pageRank(0.0001).vertices
// Join the ranks with the usernames
val users = sc.textFile("data/graphx/users.txt").map { line =>
  val fields = line.split(",")
  (fields(0).toLong, fields(1))
}
val ranksByUsername = users.join(ranks).map {
  case (id, (username, rank)) => (username, rank)
}
// Print the result
println(ranksByUsername.collect().mkString("\n"))

~~~

PageRank algorithm implementation. There are two implementations of PageRank implemented.

The first implementation uses the standalone Graph interface and runs PageRank for a fixed number of iterations:

```
var PR = Array.fill(n)( 1.0 )
val oldPR = Array.fill(n)( 1.0 )
for( iter <- 0 until numIter ) {
  swap(oldPR, PR)
  for( i <- 0 until n ) {
    PR[i] = alpha + (1 - alpha) * inNbrs[i].map(j => oldPR[j] / outDeg[j]).sum
  }
}
```
The second implementation uses the Pregel interface and runs PageRank until convergence:

```
var PR = Array.fill(n)( 1.0 )
val oldPR = Array.fill(n)( 0.0 )
while( max(abs(PR - oldPr)) > tol ) {
  swap(oldPR, PR)
  for( i <- 0 until n if abs(PR[i] - oldPR[i]) > tol ) {
    PR[i] = alpha + (1 - \alpha) * inNbrs[i].map(j => oldPR[j] / outDeg[j]).sum
  }
}
```

### [Connected Components]()

The connected components algorithm labels each connected component of the graph with the ID of its lowest-numbered vertex. For example, in a social network, connected components can approximate clusters. GraphX contains an implementation of the algorithm in the [ConnectedComponents object](http://spark.apache.org/docs/2.0.2/api/scala/index.html#org.apache.spark.graphx.lib.ConnectedComponents$), and we compute the connected components of the example social network dataset from the PageRank section as follows:

~~~
import org.apache.spark.graphx.GraphLoader

// Load the graph as in the PageRank example
val graph = GraphLoader.edgeListFile(sc, "data/graphx/followers.txt")
// Find the connected components
val cc = graph.connectedComponents().vertices
// Join the connected components with the usernames
val users = sc.textFile("data/graphx/users.txt").map { line =>
  val fields = line.split(",")
  (fields(0).toLong, fields(1))
}
val ccByUsername = users.join(cc).map {
  case (id, (username, cc)) => (username, cc)
}
// Print the result
println(ccByUsername.collect().mkString("\n"))
~~~


### [Triangle Counting](http://spark.apache.org/docs/2.0.2/graphx-programming-guide.html#triangle-counting)

A vertex is part of a triangle when it has two adjacent vertices with an edge between them. GraphX implements a triangle counting algorithm in the TriangleCount object that determines the number of triangles passing through each vertex, providing a measure of clustering. We compute the triangle count of the social network dataset from the PageRank section. Note that TriangleCount requires the edges to be in canonical orientation (srcId < dstId) and the graph to be partitioned using Graph.partitionBy.

```shell
import org.apache.spark.graphx.{GraphLoader, PartitionStrategy}

// Load the edges in canonical order and partition the graph for triangle count
val graph = GraphLoader.edgeListFile(sc, "data/graphx/followers.txt", true)
  .partitionBy(PartitionStrategy.RandomVertexCut)
// Find the triangle count for each vertex
val triCounts = graph.triangleCount().vertices
// Join the triangle counts with the usernames
val users = sc.textFile("data/graphx/users.txt").map { line =>
  val fields = line.split(",")
  (fields(0).toLong, fields(1))
}
val triCountByUsername = users.join(triCounts).map { case (id, (username, tc)) =>
  (username, tc)
}
// Print the result
println(triCountByUsername.collect().mkString("\n"))

```

### prelimery End.