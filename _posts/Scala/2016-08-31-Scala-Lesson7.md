---
layout: post
title: scala Lesson 7
description: Scala Collection
category: Language
catalog: yes
tags:
    - Scala
---
### Scala Collection

* Scala提供了一套很好的集合实现，提供了一些集合类型的抽象。
* Scala 集合分为可变的和不可变的集合。
* 可变集合可以在适当的地方被更新或扩展。这意味着你可以修改，添加，移除一个集合的元素。
* 而不可变集合类，相比之下，永远不会改变。不过，你仍然可以模拟添加，移除或更新操作。但是这些操作将在每一种情况下都返回一个新的集合，同时使原来的集合不发生改变。
* 接下来我们将为大家介绍几种常用集合类型的应用：

<table class="reference">
<tbody><tr>
<th style="width:5%">序号</th>
<th style="width:95%">集合及描述</th>
</tr>
<tr>
<td>1</td>
<td><a target="_blank" href="/language/2016/08/31/scala-lists/">Scala List(列表)</a>
<p>**List的特征是其元素以线性方式存储，集合中可以存放重复对象。** </p>
<p>参考 <a rel="nofollow" href="http://www.scala-lang.org/api/current/scala/collection/immutable/List.html" >API文档</a></p>
</td>
</tr>
<tr>
<td>2</td>
<td><a target="_blank" href="/language/2016/08/31/scala-sets/">Scala Set(集合) </a>
<p>Set是最简单的一种集合。集合中的对象不按特定的方式排序，并且没有重复对象。</p>
<p>参考 <a target="_blank" rel="nofollow" href="http://www.scala-lang.org/api/current/scala/collection/immutable/Set.html" >API文档</a></p>
</td>
</tr>
<tr>
<td>3</td>
<td><a target="_blank" href="/language/2016/08/31/scala-maps/">Scala Map(映射)</a>
<p>Map 是一种把键对象和值对象映射的集合，它的每一个元素都包含一对键对象和值对象。 </p>
<p>参考 <a target="_blank" rel="nofollow" href="http://www.scala-lang.org/api/current/scala/collection/immutable/Map.html" >API文档</a></p>
</td>
</tr>
<tr>
<td>4</td>
<td><a target="_blank" href="/language/2016/08/31/scala-tuples/">Scala 元组</a>
<p>元组是不同类型的值的集合</p></td>
</tr>
<tr>
<td>5</td>
<td><a target="_blank" href="/language/2016/08/31/scala-options/">Scala Option</a>
<p>Option[T] 表示有可能包含值的容器，也可能不包含值。</p></td>
</tr>
<tr>
<td>6</td>
<td><a target="_blank" href="/language/2016/08/31/scala-iterators/">Scala Iterator（迭代器）</a>
<p>迭代器不是一个容器，更确切的说是逐一访问容器内元素的方法。</p></td>
</tr>
</tbody></table>


#### 1 实例


* **实例**
* 以下代码判断，演示了所有以上集合类型的定义实例：

~~~scala
// 定义整型 List
val x = List(1,2,3,4)

// 定义 Set
var x = Set(1,3,5,7)

// 定义 Map
val x = Map("one" -> 1, "two" -> 2, "three" -> 3)

// 创建两个不同类型元素的元组
val x = (10, "Runoob")

// 定义 Option
val x:Option[Int] = Some(5)
~~~
