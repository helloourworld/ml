---
layout: post
title: Scala Set
description: Scala Set(集合)
category: Language
catalog: yes
tags:
    - Scala
---
### Scala Set(集合)

* Scala Set(集合)是没有重复的对象集合，所有的元素都是唯一的。
* Scala 集合分为可变的和不可变的集合。
* 默认情况下，Scala 使用的是不可变集合，如果你想使用可变集合，需要引用 scala.collection.mutable.Set 包。
* 默认引用 scala.collection.immutable.Set，不可变集合实例如下：

~~~scala
scala> val a = Set(1,2,3)
a: scala.collection.immutable.Set[Int] = Set(1, 2, 3)

scala> print(a.getClass.getName) //
scala.collection.immutable.Set$Set3
scala> println(a.exists(_ % 2 == 0)) //true
true

scala> println(a.drop(1)) //Set(2,3)
Set(2, 3)
~~~

如果需要使用可变集合需要引入 scala.collection.mutable.Set：

~~~scala
import scala.collection.mutable.Set // 可以在任何地方引入 可变集合

val mutableSet = Set(1,2,3)
println(mutableSet.getClass.getName) // scala.collection.mutable.HashSet

mutableSet.add(4)
mutableSet.remove(1)
mutableSet += 5
mutableSet -= 2

println(mutableSet) // Set(5, 3, 4)

val another = mutableSet.toSet
println(another.getClass.getName) // scala.collection.immutable.Set
~~~

`注意： 虽然可变Set和不可变Set都有添加或删除元素的操作，但是有一个非常大的差别。对不可变Set进行操作，会产生一个新的set，原来的set并没有改变，这与List一样。 而对可变Set进行操作，改变的是该Set本身，与ListBuffer类似。`

#### 1 集合基本操作

Scala集合有三个基本操作：

* \* head 返回集合第一个元素
* \* tail 返回一个集合，包含**除了第一元素之外的其他元素**
* \* isEmpty 在集合为空时返回true

对于Scala集合的任何操作都可以使用这三个基本操作来表达。实例如下:

~~~scala
object Sets1 {
   def main(args: Array[String]) {
      val site = Set("Runoob", "Google", "Baidu")
      val nums: Set[Int] = Set()

      println( "第一网站是 : " + site.head )
      println( "最后一个网站是 : " + site.tail )
      println( "查看列表 site 是否为空 : " + site.isEmpty )
      println( "查看 nums 是否为空 : " + nums.isEmpty )
   }
}
~~~

#### 2 连接集合

你可以使用 ++ 运算符或 Set.++() 方法来连接两个集合。
如果元素有重复的就会移除重复的元素。实例如下：

~~~scala
object Sets2 {
   def main(args: Array[String]) {
      val site1 = Set("Runoob", "Google", "Baidu")
      val site2 = Set("Faceboook", "Taobao")

      // ++ 作为运算符使用
      var site = site1 ++ site2
      println( "site1 ++ site2 : " + site )

      //  ++ 作为方法使用
      site = site1.++(site2)
      println( "site1.++(site2) : " + site )
   }
}
~~~

#### 3 查找集合中最大与最小元素

你可以使用 Set.min 方法来查找集合中的最小元素，使用 Set.max 方法查找集合中的最大元素。实例如下：

~~~scala
object Sets3 {
   def main(args: Array[String]) {
      val num = Set(5,6,9,20,30,45)

      // 查找集合中最大与最小元素
      println( "Set(5,6,9,20,30,45) 集合中的最小元素是 : " + num.min )
      println( "Set(5,6,9,20,30,45) 集合中的最大元素是 : " + num.max )
   }
}
~~~

#### 4 交集

你可以使用 Set.& 方法或 Set.intersect 方法来查看两个集合的交集元素。实例如下：

~~~scala
object Sets4 {
   def main(args: Array[String]) {
      val num1 = Set(5,6,9,20,30,45)
      val num2 = Set(50,60,9,20,35,55)

      // 交集
      println( "num1.&(num2) : " + num1.&(num2) )
      println( "num1.intersect(num2) : " + num1.intersect(num2) )
   }
}
~~~

#### [返回 Scala-Lesson7](/language/2016/08/31/Scala-Lesson7/)

#### 5 Scala Set 常用方法

下表列出了 Scala Set 常用的方法：

<table class="reference">
<tbody><tr>
<th style="width:5%">序号</th>
<th style="width:95%">方法及描述</th>
</tr>
<tr>
<td>1</td>
<td>
<p><b>def +(elem: A): Set[A]</b></p>
<p>为集合添加新元素，x并创建一个新的集合，除非元素已存在</p>
</td>
</tr>
<tr>
<td>2</td>
<td>
<p><b>def -(elem: A): Set[A]</b></p>
<p>移除集合中的元素，并创建一个新的集合</p>
</td>
</tr>
<tr>
<td>3</td>
<td>
<p><b>def contains(elem: A): Boolean</b></p>
<p>如果元素在集合中存在，返回 true，否则返回 false。</p>
</td>
</tr>
<tr>
<td>4</td>
<td>
<p><b>def &amp;(that: Set[A]): Set[A]</b></p>
<p>返回两个集合的交集</p>
</td>
</tr>
<tr>
<td>5</td>
<td>
<p><b>def &amp;~(that: Set[A]): Set[A]</b></p>
<p>返回两个集合的差集</p>
</td>
</tr>
<tr>
<td>6</td>
<td>
<p><b>def +(elem1: A, elem2: A, elems: A*): Set[A]</b></p>
<p>通过添加传入指定集合的元素创建一个新的不可变集合</p>
</td>
</tr>
<tr>
<td>7</td>
<td>
<p><b>def ++(elems: A): Set[A]</b></p>
<p>合并两个集合</p>
</td>
</tr>
<tr>
<td>8</td>
<td>
<p><b>def -(elem1: A, elem2: A, elems: A*): Set[A]</b></p>
<p>通过移除传入指定集合的元素创建一个新的不可变集合</p>
</td>
</tr>
<tr>
<td>9</td>
<td>
<p><b>def addString(b: StringBuilder): StringBuilder</b></p>
<p>将不可变集合的所有元素添加到字符串缓冲区</p>
</td>
</tr>
<tr>
<td>10</td>
<td>
<p><b>def addString(b: StringBuilder, sep: String): StringBuilder</b></p>
<p>将不可变集合的所有元素添加到字符串缓冲区，并使用指定的分隔符</p>
</td>
</tr>
<tr>
<td>11</td>
<td>
<p><b>def apply(elem: A)</b></p>
<p>检测集合中是否包含指定元素</p>
</td>
</tr>
<tr>
<td>12</td>
<td>
<p><b>def count(p: (A) =&gt; Boolean): Int</b></p>
<p>计算满足指定条件的集合元素个数</p>
</td>
</tr>
<tr>
<td>13</td>
<td>
<p><b>def copyToArray(xs: Array[A], start: Int, len: Int): Unit</b></p>
<p>复制不可变集合元素到数组</p>
</td>
</tr>
<tr>
<td>14</td>
<td>
<p><b>def diff(that: Set[A]): Set[A]</b></p>
<p>比较两个集合的差集</p>
</td>
</tr>
<tr>
<td>15</td>
<td>
<p><b>def drop(n: Int): Set[A]]</b></p>
<p>返回丢弃前n个元素新集合</p>
</td>
</tr>
<tr>
<td>16</td>
<td>
<p><b>def dropRight(n: Int): Set[A]</b></p>
<p>返回丢弃最后n个元素新集合</p>
</td>
</tr>
<tr>
<td>17</td>
<td>
<p><b>def dropWhile(p: (A) =&gt; Boolean): Set[A]</b></p>
<p>从左向右丢弃元素，直到条件p不成立</p>
<p></p>
</td>
</tr>
<tr>
<td>18</td>
<td>
<p><b>def equals(that: Any): Boolean</b></p>
<p>equals 方法可用于任意序列。用于比较系列是否相等。</p>
</td>
</tr>
<tr>
<td>19</td>
<td>
<p><b>def exists(p: (A) =&gt; Boolean): Boolean</b></p>
<p>判断不可变集合中指定条件的元素是否存在。</p>
</td>
</tr>
<tr>
<td>20</td>
<td>
<p><b>def filter(p: (A) =&gt; Boolean): Set[A]</b></p>
<p>输出符合指定条件的所有不可变集合元素。</p>
</td>
</tr>
<tr>
<td>21</td>
<td>
<p><b>def find(p: (A) =&gt; Boolean): Option[A]</b></p>
<p>查找不可变集合中满足指定条件的第一个元素</p>
</td>
</tr>
<tr>
<td>22</td>
<td>
<p><b>def forall(p: (A) =&gt; Boolean): Boolean</b></p>
<p>查找不可变集合中满足指定条件的所有元素</p>
</td>
</tr>
<tr>
<td>23</td>
<td>
<p><b>def foreach(f: (A) =&gt; Unit): Unit</b></p>
<p>将函数应用到不可变集合的所有元素</p>
</td>
</tr>
<tr>
<td>24</td>
<td>
<p><b>def head: A</b></p>
<p>获取不可变集合的第一个元素</p>
</td>
</tr>
<tr>
<td>25</td>
<td>
<p><b>def init: Set[A]</b></p>
<p>返回所有元素，除了最后一个</p>
</td>
</tr>
<tr>
<td>26</td>
<td>
<p><b>def intersect(that: Set[A]): Set[A]</b></p>
<p>计算两个集合的交集</p>
</td>
</tr>
<tr>
<td>27</td>
<td>
<p><b>def isEmpty: Boolean</b></p>
<p>判断集合是否为空</p>
</td>
</tr>
<tr>
<td>28</td>
<td>
<p><b>def iterator: Iterator[A]</b></p>
<p>创建一个新的迭代器来迭代元素</p>
</td>
</tr>
<tr>
<td>29</td>
<td>
<p><b>def last: A</b></p>
<p>返回最后一个元素</p>
</td>
</tr>
<tr>
<td>30</td>
<td>
<p><b>def map[B](f: (A) =&gt; B): immutable.Set[B]</b></p>
<p>通过给定的方法将所有元素重新计算</p>
</td>
</tr>
<tr>
<td>31</td>
<td>
<p><b>def max: A</b></p>
<p>查找最大元素</p>
</td>
</tr>
<tr>
<td>32</td>
<td>
<p><b>def min: A</b></p>
<p>查找最小元素</p>
</td>
</tr>
<tr>
<td>33</td>
<td>
<p><b>def mkString: String</b></p>
<p>集合所有元素作为字符串显示</p>
</td>
</tr>
<tr>
<td>34</td>
<td>
<p><b>def mkString(sep: String): String</b></p>
<p>使用分隔符将集合所有元素作为字符串显示</p>
</td>
</tr>
<tr>
<td>35</td>
<td>
<p><b>def product: A</b></p>
<p>返回不可变集合中数字元素的积。</p>
</td>
</tr>
<tr>
<td>36</td>
<td>
<p><b>def size: Int</b></p>
<p>返回不可变集合元素的数量</p>
</td>
</tr>
<tr>
<td>37</td>
<td>
<p><b>def splitAt(n: Int): (Set[A], Set[A])</b></p>
<p>把不可变集合拆分为两个容器，第一个由前 n 个元素组成，第二个由剩下的元素组成</p>
</td>
</tr>
<tr>
<td>38</td>
<td>
<p><b>def subsetOf(that: Set[A]): Boolean</b></p>
<p>如果集合中含有子集返回 true，否则返回false</p>
</td>
</tr>
<tr>
<td>39</td>
<td>
<p><b>def sum: A</b></p>
<p>返回不可变集合中所有数字元素之和</p>
</td>
</tr>
<tr>
<td>40</td>
<td>
<p><b>def tail: Set[A]</b></p>
<p>返回一个不可变集合中除了第一元素之外的其他元素</p>
</td>
</tr>
<tr>
<td>41</td>
<td>
<p><b>def take(n: Int): Set[A]</b></p>
<p>返回前 n 个元素</p>
</td>
</tr>
<tr>
<td>42</td>
<td>
<p><b>def takeRight(n: Int):Set[A]</b></p>
<p>返回后 n 个元素</p>
</td>
</tr>
<tr>
<td>43</td>
<td>
<p><b>def toArray: Array[A]</b></p>
<p>将集合转换为数字</p>
</td>
</tr>
<tr>
<td>44</td>
<td>
<p><b>def toBuffer[B &gt;: A]: Buffer[B]</b></p>
<p>返回缓冲区，包含了不可变集合的所有元素</p>
</td>
</tr>
<tr>
<td>45</td>
<td>
<p><b>def toList: List[A]</b></p>
<p>返回 List，包含了不可变集合的所有元素</p>
</td>
</tr>
<tr>
<td>46</td>
<td>
<p><b>def toMap[T, U]: Map[T, U]</b></p>
<p>返回 Map，包含了不可变集合的所有元素</p>
</td>
</tr>
<tr>
<td>47</td>
<td>
<p><b>def toSeq: Seq[A]</b></p>
<p>返回 Seq，包含了不可变集合的所有元素</p>
</td>
</tr>
<tr>
<td>48</td>
<td>
<p><b>def toString(): String</b></p>
<p>返回一个字符串，以对象来表示</p>
</td>
</tr>
</tbody></table>
<p>更多方法可以参考 <a rel="nofollow" href="http://www.scala-lang.org/api/current/scala/collection/immutable/List.html" target="_blank">API文档</a></p>










