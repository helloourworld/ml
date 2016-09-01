---
layout: post
title: Scala Option
description: Scala Option(选项)
category: Language
catalog: yes
tags:
    - Scala
---
### Scala Option(选项)

* Scala Option(选项)类型用来表示一个值是可选的（有值或无值)。
* Option[T] 是一个类型为 T 的可选值的容器： 如果值存在， Option[T] 就是一个 Some[T] ，如果不存在， Option[T] 就是对象 None 。
* 接下来我们来看一段代码：

~~~scala
// 虽然 Scala 可以不定义变量的类型，不过为了清楚些，我还是
// 把他显示的定义上了

val myMap: Map[String, String] = Map("key1" -> "value")
val value1: Option[String] = myMap.get("key1")
val value2: Option[String] = myMap.get("key2")

println(value1) // Some("value1")
println(value2) // None
~~~

* 在上面的代码中，myMap 是一个 Key 的类型是 String，Value 的类型是 String 的 hash map，但不一样的是他的 get() 返回的是一个叫 Option[String] 的类别。
* Scala 使用 Option[String] 来告诉你：「我会想办法回传一个 String，但也可能没有 String 给你」。
* myMap 里并没有 key2 这笔数据，get() 方法返回 None。
* Option 有两个子类别，一个是 **Some**，一个是 **None**，当他回传 Some 的时候，代表这个函式成功地给了你一个 String，而你可以透过 get() 这个函式拿到那个 String，如果他返回的是 None，则代表没有字符串可以给你。
* 另一个实例：

~~~scala
object Options1 {
   def main(args: Array[String]) {
      val sites = Map("runoob" -> "www.runoob.com", "google" -> "www.google.com")

      println("sites.get( \"runoob\" ) : " +  sites.get( "runoob" )) // Some(www.runoob.com)
      println("sites.get( \"baidu\" ) : " +  sites.get( "baidu" ))  //  None
   }
}
~~~

result:

~~~sublime
sites.get( "runoob" ) : Some(www.runoob.com)
sites.get( "baidu" ) : None
~~~

#### 1 模式匹配

你也可以通过模式匹配来输出匹配值。实例如下：

~~~scala
object Options2 {
   def main(args: Array[String]) {
      val sites = Map("runoob" -> "www.runoob.com", "google" -> "www.google.com")

      println("show(sites.get( \"runoob\")) : " +
                                          show(sites.get( "runoob")) )
      println("show(sites.get( \"baidu\")) : " +
                                          show(sites.get( "baidu")) )
   }

   def show(x: Option[String]) = x match {
      case Some(s) => s
      case None => "?"
   }
}
# result
show(sites.get( "runoob")) : www.runoob.com
show(sites.get( "baidu")) : ?
~~~

#### 2 getOrElse() 方法

你可以使用 getOrElse() 方法来获取元组中存在的元素或者使用其默认的值，实例如下：

~~~scala
object Options3 {
   def main(args: Array[String]) {

      val a:Option[Int] = Some(5)
      val b:Option[Int] = None

      println("a.getOrElse(0): " + a.getOrElse(0) )
      println("b.getOrElse(10): " + b.getOrElse(10) )
   }
}
~~~

#### 3 isEmpty() 方法

你可以使用 isEmpty() 方法来检测元组中的元素是否为 None，实例如下：

~~~scala
object Options4 {
   def main(args: Array[String]) {

      val a:Option[Int] = Some(5)
      val b:Option[Int] = None

      println("a.isEmpty: " + a.isEmpty )
      println("b.isEmpty: " + b.isEmpty )
   }
}
~~~

#### 4 Scala Option 常用方法

下表列出了 Scala Option 常用的方法：

<table>
<tbody><tr>
<th style="width:5%">序号</th>
<th style="width:95%">方法及描述</th>
</tr>
<tr>
<td>1</td>
<td>
<p><b>def get: A</b></p>
<p>获取可选值</p>
</td>
</tr>
<tr>
<td>2</td>
<td>
<p><b>def isEmpty: Boolean</b></p>
<p>检测可选类型值是否为 None，是的话返回 true，否则返回 false</p>
</td>
</tr>
<tr>
<td>3</td>
<td>
<p><b>def productArity: Int</b></p>
<p>返回元素个数， A(x_1, ..., x_k), 返回 k</p>
</td>
</tr>
<tr>
<td>4</td>
<td>
<p><b>def productElement(n: Int): Any</b></p>
<p>获取指定的可选项，以 0 为起始。即 A(x_1, ..., x_k), 返回 x_(n+1) ， 0 &lt; n &lt; k.</p>
</td>
</tr>
<tr>
<td>5</td>
<td>
<p><b>def exists(p: (A) =&gt; Boolean): Boolean</b></p>
<p>如果可选项中指定条件的元素是否存在且不为 None 返回 true，否则返回 false。</p>
</td>
</tr>
<tr>
<td>6</td>
<td>
<p><b>def filter(p: (A) =&gt; Boolean): Option[A]</b></p>
<p>如果选项包含有值，而且传递给 filter 的条件函数返回 true， filter 会返回 Some 实例。 否则，返回值为 None 。</p>
</td>
</tr>
<tr>
<td>7</td>
<td>
<p><b>def filterNot(p: (A) =&gt; Boolean): Option[A]</b></p>
<p>如果选项包含有值，而且传递给 filter 的条件函数返回 false， filter 会返回 Some 实例。 否则，返回值为 None 。</p>
</td>
</tr>
<tr>
<td>8</td>
<td>
<p><b>def flatMap[B](f: (A) =&gt; Option[B]): Option[B]</b></p>
<p>如果选项包含有值，则传递给函数 f 处理后返回，否则返回 None</p>
</td>
</tr>
<tr>
<td>9</td>
<td>
<p><b>def foreach[U](f: (A) =&gt; U): Unit</b></p>
<p>如果选项包含有值，则将每个值传递给函数 f， 否则不处理。 </p>
</td>
</tr>
<tr>
<td>10</td>
<td>
<p><b>def getOrElse[B &gt;: A](default: =&gt; B): B</b></p>
<p>如果选项包含有值，返回选项值，否则返回设定的默认值。</p>
</td>
</tr>
<tr>
<td>11</td>
<td>
<p><b>def isDefined: Boolean</b></p>
<p>如果可选值是 Some 的实例返回 true，否则返回 false。 </p>
</td>
</tr>
<tr>
<td>12</td>
<td>
<p><b>def iterator: Iterator[A]</b></p>
<p>如果选项包含有值，迭代出可选值。如果可选值为空则返回空迭代器。 </p>
</td>
</tr>
<tr>
<td>13</td>
<td>
<p><b>def map[B](f: (A) =&gt; B): Option[B]</b></p>
<p>如果选项包含有值， 返回由函数 f 处理后的 Some，否则返回 None</p>
</td>
</tr>
<tr>
<td>14</td>
<td>
<p><b>def orElse[B &gt;: A](alternative: =&gt; Option[B]): Option[B]</b></p>
<p>如果一个 Option 是 None ， orElse 方法会返回传名参数的值，否则，就直接返回这个 Option。</p>
</td>
</tr>
<tr>
<td>15</td>
<td>
<p><b>def orNull</b></p>
<p>如果选项包含有值返回选项值，否则返回 null。</p>
</td>
</tr>
</tbody></table>
