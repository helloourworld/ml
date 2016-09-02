---
layout: post
title: Scala Lesson 11
description: Scala 模式匹配, scala-pattern-matching
category: Language
catalog: yes
tags:
    - Scala
---
### Scala 模式匹配

* Scala 提供了强大的模式匹配机制，应用也非常广泛。
* 一个模式匹配包含了一系列备选项，每个都开始于关键字 case。每个备选项都包含了一个模式及一到多个表达式。箭头符号 => 隔开了模式和表达式。
* 以下是一个简单的整型值模式匹配实例：

~~~scala
object PatternMatching1 {
    def main (args: Array[String]) {
        println(matchTest(2))
    }

    def matchTest(x: Int): String = x match {
        case 1 => "one"
        case 2 => "tow"
        case _ => "many"
    }
}
~~~

* match 对应 Java 里的 switch，但是写在选择器表达式之后。即： **选择器 match {备选项}**。
* match 表达式通过以代码编写的**先后次序尝试每个模式来完成计算**，只要发现有一个匹配的case，剩下的case不会继续匹配。
* 接下来我们来看一个不同数据类型的模式匹配：

~~~scala
object PatternMatching2 {
    def main (args: Array[String]) {
        println(matchTest("two"))
        println(matchTest("2"))
        println(matchTest(1))
        println(matchTest(2))

    }

    def matchTest(x: Any): Any = x match {
        case 1 => "one"
        case "two" => 2
        case y: Int => "scala.Int"
        case _ => "many"
    }
}
# output
2
many
one
scala.Int
~~~

### 使用样例类

* 使用了**case**关键字的类定义就是就是样例类(case classes)，样例类是种特殊的类，经过优化以用于模式匹配。
* 以下是样例类的简单实例:

~~~scala
object PatternMatching3 {
    def main(args: Array[String]) {
        val alice = new Person("Alice", 28)
        val baobao = new Person("Baobao", 29)
        val charlie = new Person("Charlie", 32)

        for (person <- List(alice, baobao, charlie)) {
            person match {
                case Person("Alice", 28) => println("Hi Alice!")
                case Person("Baobao", 29) => println("Hi, Baobao")
                case Person(name, age) => println("Age: " + age + " year, name: " +name + "?")
            }
        }
    }
    //样例类
    case class Person(name: String, age: Int)
}

# output
Hi Alice!
Hi, Baobao
Age: 32 year, name: Charlie?
~~~

在声明样例类时，下面的过程自动发生了：

>* 构造器的每个参数都成为val，除非显式被声明为var，但是并不推荐这么做；
>* 在伴生对象中提供了apply方法，所以可以不使用new关键字就可构建对象；
>* 提供unapply方法使模式匹配可以工作；
>* 生成toString、equals、hashCode和copy方法，除非显示给出这些方法的定义。
