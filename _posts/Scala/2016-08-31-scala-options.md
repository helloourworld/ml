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
