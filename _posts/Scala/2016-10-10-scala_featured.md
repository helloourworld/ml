---
layout: post
title: Scala Featured
description: scala中"_"的用法总结
category: Language
catalog: yes
tags:
    - Scala
---

### 1 scala中"_"的用法总结

参考链接：

[what-are-all-the-uses-of-an-underscore-in-scala](http://stackoverflow.com/questions/8000903/what-are-all-the-uses-of-an-underscore-in-scala)

[http://www.slideshare.net/normation/scala-dreaded](http://www.slideshare.net/normation/scala-dreaded)

~~~
import scala._    // Wild card -- all of Scala is imported
import scala.{ Predef => _, _ } // Exception, everything except Predef
def f[M[_]]       // Higher kinded type parameter
def f(m: M[_])    // Existential type
_ + _             // Anonymous function placeholder parameter
m _               // Eta expansion of method into method value
m(_)              // Partial function application
_ => 5            // Discarded parameter
case _ =>         // Wild card pattern -- matches anything
val (a, _) = (1, 2) // same thing
for (_ <- 1 to 10)  // same thing
f(xs: _*)         // Sequence xs is passed as multiple parameters to f(ys: T*)
case Seq(xs @ _*) // Identifier xs is bound to the whole matched sequence
var i: Int = _    // Initialization to the default value
def abc_<>!       // An underscore must separate alphanumerics from symbols on identifiers
t._2              // Part of a method name, such as tuple getters
~~~

**Existential types**\\
```def foo(l: List[Option[_]]) = ...```

**Higher kinded type parameters**\\
```case class A[K[_],T](a: K[T])```

**Ignored variables**\\
```val _ = 5```


**Ignored parameters**\\
```List(1, 2, 3) foreach { _ => println("Hi") }```


**Ignored names of self types**\\
```trait MySeq { _: Seq[_] => }```


**Wildcard patterns**\\
```Some(5) match { case Some(_) => println("Yes") }```


**Wildcard imports**\\
```import java.util._```


**Hiding imports**\\
```import java.util.{ArrayList => _, _}```


**Joining letters to punctuation**\\
```def bang_!(x: Int) = 5```


**Assignment operators**\\
```def foo_=(x: Int) { ... }```


**Placeholder syntax**\\
```List(1, 2, 3) map (_ + 2)```


**Partially applied functions**\\
```List(1, 2, 3) foreach println _```


**Converting call-by-name parameters to functions**\\
```def toFunction(callByName: => Int): () => Int = callByName _```

