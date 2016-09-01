---
layout: post
title: Scala Map
description: Scala Map(映射)
category: Language
catalog: yes
tags:
    - Scala
---
### Scala Map(映射)

* Map(映射)是一种可迭代的键值对（key/value）结构。
* 所有的值都可以通过键来获取。
* Map 中的键都是唯一的。
* Map 也叫哈希表（Hash tables）。
* Map 有两种类型，可变与不可变，区别在于可变对象可以修改它，而不可变对象不可以。
* 默认情况下 Scala 使用不可变 Map。如果你需要使用可变集合，你需要显式的引入 import scala.collection.mutable.Map 类
* 在 Scala 中 你可以同时使用可变与不可变 Map，不可变的直接使用 Map，可变的使用 mutable.Map。以下实例演示了不可变 Map 的应用：

~~~scala
// 空哈希表，键为字符串，值为整型
var A:Map[Char,Int] = Map()

// Map 键值对演示
val colors = Map("red" -> "#FF0000", "azure" -> "#F0FFFF")
~~~

定义 Map 时，需要为键值对定义类型。如果需要添加 key-value 对，可以使用 + 号，如下所示：

~~~scala
A += ('I' -> 1)
A += ('J' -> 5)
A += ('K' -> 10)
A += ('L' -> 100)
~~~

#### 1 Map 基本操作

Scala Map 有三个基本操作：

方法|  描述
keys |   返回 Map 所有的键(key)
values | 返回 Map 所有的值(value)
isEmpty |在 Map 为空时返回true

**Example**

~~~scala
object Maps1{
    def main(args: Array[String]) {
        val colors = Map("red" -> "#FF0000",
                                    "azure" -> "#F0FFFF",
                                    "peru" -> "#CD853F")

        val nums: Map[Int, Int] = Map()

        println("colors中的键为：" + colors.keys)
        println("colors中的值为：" + colors.values)
        println("检测colors是否为空：" + colors.isEmpty)
        println("检测nums是否为空：" + nums.isEmpty)
    }
}
~~~

输出结果：

~~~scala
colors中的键为：Set(red, azure, peru)
colors中的值为：MapLike(#FF0000, #F0FFFF, #CD853F)
检测colors是否为空：false
检测nums是否为空：true
~~~

#### 2 Map合并

你可以使用 ++ 运算符或 Map.++() 方法来连接两个 Map，Map 合并时会移除重复的 key。以下演示了两个 Map 合并的实例:

~~~scala
object Maps2{
    def main(args: Array[String]) {
        val colors1 = Map("red" -> "#FF0000",
                        "azure" -> "#F0FFFF",
                        "peru" -> "#CD853F")
        val colors2 = Map("blue" -> "#0033FF",
                        "yellow" -> "#FFFF00",
                        "red" -> "#FF0000")

        // ++ 作为运算符
        var colors = colors1 ++ colors2
        println("colors1 ++ colors2: " + colors)

        // .++ 作为方法
        colors = colors1.++(colors2)
        println("colors1.++(colors2: " + colors)
    }
}
~~~

#### 3 输出 Map 的 keys 和 values

以下通过 foreach 循环输出 Map 中的 keys 和 values：

~~~scala
object Maps3{
   def main(args: Array[String]) {
      val sites = Map("runoob" -> "http://www.runoob.com",
                       "baidu" -> "http://www.baidu.com",
                       "taobao" -> "http://www.taobao.com")

      sites.keys.foreach{ i =>
                           print( "Key = " + i )
                           println(" Value = " + sites(i) )}
   }
}
#output
Key = runoob Value = http://www.runoob.com
Key = baidu Value = http://www.baidu.com
Key = taobao Value = http://www.taobao.com
~~~

#### 4 查看 Map 中是否存在指定的 Key

你可以使用 Map.contains 方法来查看 Map 中是否存在指定的 Key。实例如下：

~~~scala
object Maps4{
   def main(args: Array[String]) {
      val sites = Map("runoob" -> "http://www.runoob.com",
                       "baidu" -> "http://www.baidu.com",
                       "taobao" -> "http://www.taobao.com")

      if( sites.contains( "runoob" )){
           println("runoob 键存在，对应的值为 :"  + sites("runoob"))
      }else{
           println("runoob 键不存在")
      }
      if( sites.contains( "baidu" )){
           println("baidu 键存在，对应的值为 :"  + sites("baidu"))
      }else{
           println("baidu 键不存在")
      }
      if( sites.contains( "google" )){
           println("google 键存在，对应的值为 :"  + sites("google"))
      }else{
           println("google 键不存在")
      }
   }
}
~~~

#### [返回 Scala-Lesson7](/language/2016/08/31/Scala-Lesson7/)

#### 5 Scala Map 方法

下表列出了 Scala Map 常用的方法：

<table class="reference">
<tr>
<th style="width:5%">序号</th>
<th style="width:95%">方法及描述</th>
</tr>
<tr>
<td>1</td>
<td>
<p><b>def ++(xs: Map[(A, B)]): Map[A, B]</b></p>
<p>返回一个新的 Map，新的 Map xs 组成</p>
</td>
</tr>
<tr>
<td>2</td>
<td>
<p><b>def -(elem1: A, elem2: A, elems: A*): Map[A, B]</b></p>
<p>返回一个新的 Map, 移除 key 为 elem1, elem2 或其他 elems。</p>
</td>
</tr>
<tr>
<td>3</td>
<td>
<p><b>def --(xs: GTO[A]): Map[A, B]</b></p>
<p>返回一个新的 Map,  移除 xs 对象中对应的  key</p>
</td>
</tr>
<tr>
<td>4</td>
<td>
<p><b>def get(key: A): Option[B]</b></p>
<p>返回指定 key 的值</p>
</td>
</tr>
<tr>
<td>5</td>
<td>
<p><b>def iterator: Iterator[(A, B)]</b></p>
<p>创建新的迭代器，并输出 key/value 对</p>
</td>
</tr>
<tr>
<td>6</td>
<td>
<p><b>def addString(b: StringBuilder): StringBuilder</b></p>
<p>将 Map 中的所有元素附加到StringBuilder，可加入分隔符</p>
</td>
</tr>
<tr>
<td>7</td>
<td>
<p><b>def addString(b: StringBuilder, sep: String): StringBuilder</b></p>
<p>将 Map 中的所有元素附加到StringBuilder，可加入分隔符</p>
</td>
</tr>
<tr>
<td>8</td>
<td>
<p><b>def apply(key: A): B</b></p>
<p>返回指定键的值，如果不存在返回 Map 的默认方法</p>
</td>
</tr>
<tr>
<td>9</td>
<td>
<p><b>def clear(): Unit</b></p>
<p>清空 Map</p>
</td>
</tr>
<tr>
<td>10</td>
<td>
<p><b>def clone(): Map[A, B]</b></p>
<p>从一个 Map 复制到另一个 Map</p>
</td>
</tr>
<tr>
<td>11</td>
<td>
<p><b>def contains(key: A): Boolean</b></p>
<p>如果 Map 中存在指定 key，返回 true，否则返回 false。</p>
</td>
</tr>
<tr>
<td>12</td>
<td>
<p><b>def copyToArray(xs: Array[(A, B)]): Unit</b></p>
<p>复制集合到数组 </p>
</td>
</tr>
<tr>
<td>13</td>
<td>
<p><b>def count(p: ((A, B)) =&gt; Boolean): Int</b></p>
<p>计算满足指定条件的集合元素数量</p>
</td>
</tr>
<tr>
<td>14</td>
<td>
<p><b>def default(key: A): B</b></p>
<p>定义 Map 的默认值，在 key 不存在时返回。</p>
</td>
</tr>
<tr>
<td>15</td>
<td>
<p><b>def drop(n: Int): Map[A, B]</b></p>
<p>返回丢弃前n个元素新集合</p>
</td>
</tr>
<tr>
<td>16</td>
<td>
<p><b>def dropRight(n: Int): Map[A, B]</b></p>
<p>返回丢弃最后n个元素新集合</p>
</td>
</tr>
<tr>
<td>17</td>
<td>
<p><b>def dropWhile(p: ((A, B)) =&gt; Boolean): Map[A, B]</b></p>
<p>从左向右丢弃元素，直到条件p不成立</p>
</td>
</tr>
<tr>
<td>18</td>
<td>
<p><b>def empty: Map[A, B]</b></p>
<p>返回相同类型的空 Map</p>
</td>
</tr>
<tr>
<td>19</td>
<td>
<p><b>def equals(that: Any): Boolean</b></p>
<p>如果两个 Map 相等(key/value 均相等)，返回true，否则返回false</p>
</td>
</tr>
<tr>
<td>20</td>
<td>
<p><b>def exists(p: ((A, B)) =&gt; Boolean): Boolean</b></p>
<p>判断集合中指定条件的元素是否存在</p>
</td>
</tr>
<tr>
<td>21</td>
<td>
<p><b>def filter(p: ((A, B))=&gt; Boolean): Map[A, B]</b></p>
<p>返回满足指定条件的所有集合</p>
</td>
</tr>
<tr>
<td>22</td>
<td>
<p><b>def filterKeys(p: (A) =&gt; Boolean): Map[A, B]</b></p>
<p>返回符合指定条件的的不可变 Map</p>
</td>
</tr>
<tr>
<td>23</td>
<td>
<p><b>def find(p: ((A, B)) =&gt; Boolean): Option[(A, B)]</b></p>
<p>查找集合中满足指定条件的第一个元素</p>
</td>
</tr>
<tr>
<td>24</td>
<td>
<p><b>def foreach(f: ((A, B)) =&gt; Unit): Unit</b></p>
<p>将函数应用到集合的所有元素</p>
</td>
</tr>
<tr>
<td>25</td>
<td>
<p><b>def init: Map[A, B]</b></p>
<p>返回所有元素，除了最后一个</p>
</td>
</tr>
<tr>
<td>26</td>
<td>
<p><b>def isEmpty: Boolean</b></p>
<p>检测 Map 是否为空</p>
</td>
</tr>
<tr>
<td>27</td>
<td>
<p><b>def keys: Iterable[A]</b></p>
<p>返回所有的key/p>
</td>
</tr>
<tr>
<td>28</td>
<td>
<p><b>def last: (A, B)</b></p>
<p>返回最后一个元素</p>
</td>
</tr>
<tr>
<td>29</td>
<td>
<p><b>def max: (A, B)</b></p>
<p>查找最大元素</p>
</td>
</tr>
<tr>
<td>30</td>
<td>
<p><b>def min: (A, B)</b></p>
<p>查找最小元素</p>
</td>
</tr>
<tr>
<td>31</td>
<td>
<p><b>def mkString: String</b></p>
<p>集合所有元素作为字符串显示</p>
</td>
</tr>
<tr>
<td>32</td>
<td>
<p><b>def product: (A, B)</b></p>
<p>返回集合中数字元素的积。</p>
</td>
</tr>
<tr>
<td>33</td>
<td>
<p><b>def remove(key: A): Option[B]</b></p>
<p>移除指定 key</p>
</td>
</tr>
<tr>
<td>34</td>
<td>
<p><b>def retain(p: (A, B) =&gt; Boolean): Map.this.type</b></p>
<p>如果符合满足条件的返回 true</p>
</td>
</tr>
<tr>
<td>35</td>
<td>
<p><b>def size: Int</b></p>
<p>返回 Map 元素的个数</p>
</td>
</tr>
<tr>
<td>36</td>
<td>
<p><b>def sum: (A, B)</b></p>
<p>返回集合中所有数字元素之和</p>
</td>
</tr>
<tr>
<td>37</td>
<td>
<p><b>def tail: Map[A, B]</b></p>
<p>返回一个集合中除了第一元素之外的其他元素</p>
</td>
</tr>
<tr>
<td>38</td>
<td>
<p><b>def take(n: Int): Map[A, B]</b></p>
<p>返回前 n 个元素</p>
</td>
</tr>
<tr>
<td>39</td>
<td>
<p><b>def takeRight(n: Int): Map[A, B]</b></p>
<p>返回后 n 个元素</p>
</td>
</tr>
<tr>
<td>40</td>
<td>
<p><b>def takeWhile(p: ((A, B)) =&gt; Boolean): Map[A, B]</b></p>
<p>返回满足指定条件的元素</p>
</td>
</tr>
<tr>
<td>41</td>
<td>
<p><b>def toArray: Array[(A, B)]</b></p>
<p>集合转数组</p>
</td>
</tr>
<tr>
<td>42</td>
<td>
<p><b>def toBuffer[B &gt;: A]: Buffer[B]</b></p>
<p>返回缓冲区，包含了 Map 的所有元素</p>
</td>
</tr>
<tr>
<td>43</td>
<td>
<p><b>def toList: List[A]</b></p>
<p>返回 List，包含了 Map 的所有元素</p>
</td>
</tr>
<tr>
<td>44</td>
<td>
<p><b>def toSeq: Seq[A]</b></p>
<p>返回 Seq，包含了 Map 的所有元素</p>
</td>
</tr>
<tr>
<td>45</td>
<td>
<p><b>def toSet: Set[A]</b></p>
<p>返回 Set，包含了 Map 的所有元素</p>
</td>
</tr>
<tr>
<td>46</td>
<td>
<p><b>def toString(): String</b></p>
<p>返回字符串对象</p>
</td>
</tr>
<p>更多方法可以参考 <a target="_blank" rel="nofollow" href="http://www.scala-lang.org/api/current/scala/collection/immutable/Map.html">API文档</a></p>

