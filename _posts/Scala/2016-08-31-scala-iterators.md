---
layout: post
title: scala Iterators
description: Scala Iterator（迭代器）
category: Language
catalog: yes
tags:
    - Scala
---
### Scala Iterator（迭代器）

* Scala Iterator（迭代器）不是一个集合，它是一种用于访问集合的方法。
* 迭代器 it 的两个基本操作是 next 和 hasNext。
* 调用 it.next() 会返回迭代器的下一个元素，并且更新迭代器的状态。
* 调用 it.hasNext() 用于检测集合中是否还有元素。
* 让迭代器 it 逐个返回所有元素最简单的方法是使用 while 循环：

~~~scala
object Iterators {
   def main(args: Array[String]) {
      val it = Iterator("Baidu", "Google")

      while (it.hasNext){
         println(it.next())
      }
   }
}
~~~

#### 查找最大与最小元素

你可以使用 it.min 和 it.max 方法从迭代器中查找最大与最小元素，实例如下:

~~~scala
object Iterators2 {
   def main(args: Array[String]) {
      val ita = Iterator(20,40,2,50,69, 90)
      val itb = Iterator(20,40,2,50,69, 90)
// Warn: 在执行完ita.max后，ita为空
      println("最大元素是：" + ita.max )
      println("最小元素是：" + itb.min )

   }
}
~~~

#### 获取迭代器的长度

你可以使用 it.size 或 it.length 方法来查看迭代器中的元素个数。实例如下：

~~~scala
object Iterators3 {
   def main(args: Array[String]) {
      val ita = Iterator(20,40,2,50,69, 90)
      val itb = Iterator(20,40,2,50,69, 90)
// Warn: 在执行完ita.size后，ita为空
      println("ita.size 的值: " + ita.size )
      println("itb.length 的值: " + itb.length )

   }
}
~~~

#### Scala Iterator 常用方法

下表列出了 Scala Iterator 常用的方法：

<table class="reference">
<tbody><tr>
<th style="width:5%">序号</th>
<th style="width:95%">方法及描述</th>
</tr>
<tr>
<td>1</td>
<td>
<p><b>def hasNext: Boolean</b></p>
<p>如果还有可返回的元素，返回true。</p>
</td>
</tr>
<tr>
<td>2</td>
<td>
<p><b>def next(): A</b></p>
<p>返回迭代器的下一个元素，并且更新迭代器的状态</p>
</td>
</tr>
<tr>
<td>3</td>
<td>
<p><b>def ++(that: =&gt; Iterator[A]): Iterator[A]</b></p>
<p>合并两个迭代器</p>
</td>
</tr>
<tr>
<td>4</td>
<td>
<p><b>def ++[B &gt;: A](that :=&gt; GenTraversableOnce[B]): Iterator[B]</b></p>
<p>合并两个迭代器</p>
</td>
</tr>
<tr>
<td>5</td>
<td>
<p><b>def addString(b: StringBuilder): StringBuilder</b></p>
<p>添加一个字符串到 StringBuilder b</p>
</td>
</tr>
<tr>
<td>6</td>
<td>
<p><b>def addString(b: StringBuilder, sep: String): StringBuilder</b></p>
<p>添加一个字符串到 StringBuilder b，并指定分隔符</p>
</td>
</tr>
<tr>
<td>7</td>
<td>
<p><b>def buffered: BufferedIterator[A]</b></p>
<p>迭代器都转换成 BufferedIterator</p>
</td>
</tr>
<tr>
<td>8</td>
<td>
<p><b>def contains(elem: Any): Boolean</b></p>
<p>检测迭代器中是否包含指定元素</p>
</td>
</tr>
<tr>
<td>9</td>
<td>
<p><b>def copyToArray(xs: Array[A], start: Int, len: Int): Unit</b></p>
<p>将迭代器中选定的值传给数组</p>
</td>
</tr>
<tr>
<td>10</td>
<td>
<p><b>def count(p: (A) =&gt; Boolean): Int</b></p>
<p>返回迭代器元素中满足条件p的元素总数。</p>
</td>
</tr>
<tr>
<td>11</td>
<td>
<p><b>def drop(n: Int): Iterator[A]</b></p>
<p>返回丢弃前n个元素新集合</p>
</td>
</tr>
<tr>
<td>12</td>
<td>
<p><b>def dropWhile(p: (A) =&gt; Boolean): Iterator[A]</b></p>
<p>从左向右丢弃元素，直到条件p不成立</p>
</td>
</tr>
<tr>
<td>13</td>
<td>
<p><b>def duplicate: (Iterator[A], Iterator[A])</b></p>
<p>生成两个能分别返回迭代器所有元素的迭代器。</p>
</td>
</tr>
<tr>
<td>14</td>
<td>
<p><b>def exists(p: (A) =&gt; Boolean): Boolean</b></p>
<p>返回一个布尔值，指明迭代器元素中是否存在满足p的元素。</p>
</td>
</tr>
<tr>
<td>15</td>
<td>
<p><b>def filter(p: (A) =&gt; Boolean): Iterator[A]</b></p>
<p>返回一个新迭代器 ，指向迭代器元素中所有满足条件p的元素。</p>
</td>
</tr>
<tr>
<td>16</td>
<td>
<p><b>def filterNot(p: (A) =&gt; Boolean): Iterator[A]</b></p>
<p>返回一个迭代器，指向迭代器元素中不满足条件p的元素。 </p>
</td>
</tr>
<tr>
<td>17</td>
<td>
<p><b>def find(p: (A) =&gt; Boolean): Option[A]</b></p>
<p>返回第一个满足p的元素或None。注意：如果找到满足条件的元素，迭代器会被置于该元素之后；如果没有找到，会被置于终点。</p>
</td>
</tr>
<tr>
<td>18</td>
<td>
<p><b>def flatMap[B](f: (A) =&gt; GenTraversableOnce[B]): Iterator[B]</b></p>
<p>针对迭代器的序列中的每个元素应用函数f，并返回指向结果序列的迭代器。 </p>
</td>
</tr>
<tr>
<td>19</td>
<td>
<p><b>def forall(p: (A) =&gt; Boolean): Boolean</b></p>
<p>返回一个布尔值，指明 it 所指元素是否都满足p。</p>
</td>
</tr>
<tr>
<td>20</td>
<td>
<p><b>def foreach(f: (A) =&gt; Unit): Unit</b></p>
<p>在迭代器返回的每个元素上执行指定的程序 f</p>
</td>
</tr>
<tr>
<td>21</td>
<td>
<p><b>def hasDefiniteSize: Boolean</b></p>
<p>如果迭代器的元素个数有限则返回true（缺省等同于isEmpty） </p>
</td>
</tr>
<tr>
<td>22</td>
<td>
<p><b>def indexOf(elem: B): Int</b></p>
<p>返回迭代器的元素中index等于x的第一个元素。注意：迭代器会越过这个元素。</p>
</td>
</tr>
<tr>
<td>23</td>
<td>
<p><b>def indexWhere(p: (A) =&gt; Boolean): Int</b></p>
<p>返回迭代器的元素中下标满足条件p的元素。注意：迭代器会越过这个元素。</p>
</td>
</tr>
<tr>
<td>24</td>
<td>
<p><b>def isEmpty: Boolean</b></p>
<p>检查it是否为空, 为空返回 true，否则返回false（与hasNext相反）。</p>
</td>
</tr>
<tr>
<td>25</td>
<td>
<p><b>def isTraversableAgain: Boolean</b></p>
<p>Tests whether this Iterator can be repeatedly traversed.</p>
</td>
</tr>
<tr>
<td>26</td>
<td>
<p><b>def length: Int</b></p>
<p>返回迭代器元素的数量。</p>
</td>
</tr>
<tr>
<td>27</td>
<td>
<p><b>def map[B](f: (A) =&gt; B): Iterator[B]</b></p>
<p>将 it 中的每个元素传入函数 f 后的结果生成新的迭代器。</p>
</td>
</tr>
<tr>
<td>28</td>
<td>
<p><b>def max: A</b></p>
<p>返回迭代器迭代器元素中最大的元素。</p>
</td>
</tr>
<tr>
<td>29</td>
<td>
<p><b>def min: A</b></p>
<p>返回迭代器迭代器元素中最小的元素。</p>
</td>
</tr>
<tr>
<td>30</td>
<td>
<p><b>def mkString: String</b></p>
<p>将迭代器所有元素转换成字符串。</p>
</td>
</tr>
<tr>
<td>31</td>
<td>
<p><b>def mkString(sep: String): String</b></p>
<p>将迭代器所有元素转换成字符串，并指定分隔符。 </p>
</td>
</tr>
<tr>
<td>32</td>
<td>
<p><b>def nonEmpty: Boolean</b></p>
<p>检查容器中是否包含元素（相当于 hasNext）。</p>
</td>
</tr>
<tr>
<td>33</td>
<td>
<p><b>def padTo(len: Int, elem: A): Iterator[A]</b></p>
<p>首先返回迭代器所有元素，追加拷贝 elem 直到长度达到 len。</p>
</td>
</tr>
<tr>
<td>34</td>
<td>
<p><b>def patch(from: Int, patchElems: Iterator[B], replaced: Int): Iterator[B]</b></p>
<p>返回一个新迭代器，其中自第 from 个元素开始的 replaced 个元素被迭代器所指元素替换。</p>
</td>
</tr>
<tr>
<td>35</td>
<td>
<p><b>def product: A</b></p>
<p>返回迭代器所指数值型元素的积。</p>
</td>
</tr>
<tr>
<td>36</td>
<td>
<p><b>def sameElements(that: Iterator[_]): Boolean</b></p>
<p>判断迭代器和指定的迭代器参数是否依次返回相同元素</p>
</td>
</tr>
<tr>
<td>37</td>
<td>
<p><b>def seq: Iterator[A]</b></p>
<p>返回集合的系列视图</p>
</td>
</tr>
<tr>
<td>38</td>
<td>
<p><b>def size: Int</b></p>
<p>返回迭代器的元素数量</p>
</td>
</tr>
<tr>
<td>39</td>
<td>
<p><b>def slice(from: Int, until: Int): Iterator[A]</b></p>
<p>返回一个新的迭代器，指向迭代器所指向的序列中从开始于第 from 个元素、结束于第 until 个元素的片段。</p>
</td>
</tr>
<tr>
<td>40</td>
<td>
<p><b>def sum: A</b></p>
<p>返回迭代器所指数值型元素的和</p>
</td>
</tr>
<tr>
<td>41</td>
<td>
<p><b>def take(n: Int): Iterator[A]</b></p>
<p>返回前 n 个元素的新迭代器。</p>
</td>
</tr>
<tr>
<td>42</td>
<td>
<p><b>def toArray: Array[A]</b></p>
<p>将迭代器指向的所有元素归入数组并返回。</p>
</td>
</tr>
<tr>
<td>43</td>
<td>
<p><b>def toBuffer: Buffer[B]</b></p>
<p>将迭代器指向的所有元素拷贝至缓冲区 Buffer。</p>
</td>
</tr>
<tr>
<td>44</td>
<td>
<p><b>def toIterable: Iterable[A]</b></p>
<p>Returns an Iterable containing all elements of this traversable or iterator. This will not terminate for infinite iterators.</p>
</td>
</tr>
<tr>
<td>45</td>
<td>
<p><b>def toIterator: Iterator[A]</b></p>
<p>把迭代器的所有元素归入一个Iterator容器并返回。</p>
</td>
</tr>
<tr>
<td>46</td>
<td>
<p><b>def toList: List[A]</b></p>
<p>把迭代器的所有元素归入列表并返回</p>
</td>
</tr>
<tr>
<td>47</td>
<td>
<p><b>def toMap[T, U]: Map[T, U]</b></p>
<p>将迭代器的所有键值对归入一个Map并返回。</p>
</td>
</tr>
<tr>
<td>48</td>
<td>
<p><b>def toSeq: Seq[A]</b></p>
<p>将代器的所有元素归入一个Seq容器并返回。</p>
</td>
</tr>
<tr>
<td>49</td>
<td>
<p><b>def toString(): String</b></p>
<p>将迭代器转换为字符串</p>
</td>
</tr>
<tr>
<td>50</td>
<td>
<p><b>def zip[B](that: Iterator[B]): Iterator[(A, B)</b></p>
<p>返回一个新迭代器，指向分别由迭代器和指定的迭代器 that 元素一一对应而成的二元组序列</p>
</td>
</tr>
</tbody></table>


~~~
