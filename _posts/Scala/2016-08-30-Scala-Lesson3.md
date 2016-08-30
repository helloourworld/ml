---
layout: post
title: scala Lesson 3
description: IF...ELSE 语句，循环判断，
category: Language
catalog: yes
tags:
    - Scala
---
### Scala IF...ELSE 语句

#### 1 if语句

语法格式：

~~~scala
if (boole express)
{
    // 如果布尔表达式为true则执行该语句块；否则跳过大括号内的语句块，执行大括号之后的语句块。
}

# ex
object Ifs {
    def main(args: Array[String]){
        var x = 10;
        if (x < 20){
            println(" x is less than 20");
        }
    }
}
~~~

#### 2 if...else 语句
if 语句后可以紧跟 else 语句，else 内的语句块可以在布尔表达式为 false 的时候执行。

语法格式：

~~~scala
if(布尔表达式){
   // 如果布尔表达式为 true 则执行该语句块
}else{
   // 如果布尔表达式为 false 则执行该语句块
}
# ex
object Ifs {
    def main(args: Array[String]){
        var x = 30;

        if (x < 20){
            println(" x is less than 20");
        }else{
            println("x is larger or eq 20")
        }
    }
}
~~~

#### 3 if...else if...else 语句

语法格式：

~~~scala
if(布尔表达式 1){
   // 如果布尔表达式 1 为 true 则执行该语句块
}else if(布尔表达式 2){
   // 如果布尔表达式 2 为 true 则执行该语句块
}else if(布尔表达式 3){
   // 如果布尔表达式 3 为 true 则执行该语句块
}else {
   // 如果以上条件都为 false 执行该语句块
}
# ex
object Ifs {
    def main(args: Array[String]){
        var x = 30;

        if (x < 20){
            println(" x is less than 20");
        }else if (x == 20){
            println("x is leq 20")
        }else {
            println ("x is larger than 20")
        }
    }
}
~~~

#### 4 if...else 嵌套语句

语法格式：

~~~scala
if(布尔表达式 1){
   // 如果布尔表达式 1 为 true 则执行该语句块
   if(布尔表达式 2){
      // 如果布尔表达式 2 为 true 则执行该语句块
   }
}
# ex
object Ifs {
    def main(args: Array[String]){
        var x = 30;
        var y = 20;
        if (y > 0){
          if (x > 0){
            println("x / y = " + (x / y));
          }
        }
    }
}
~~~

### 循环

有的时候，我们可能需要多次执行同一块代码。一般情况下，语句是按顺序执行的：函数中的第一个语句先执行，接着是第二个语句，依此类推。
编程语言提供了更为复杂执行路径的多种控制结构。
循环语句允许我们多次执行一个语句或语句组，下面是大多数编程语言中循环语句的流程图：
![](/images/scala/loop.png)

#### 1 while 循环
只要给定的条件为 true，Scala 语言中的 while 循环语句会重复执行循环体内的代码块。最少执行0次。

语法格式：

~~~scala
while(condition)
{
    statement(s);
}
# ex
object Loops {
    def main(args: Array[String]) {
        // 局部变量
        var a = 10;
        // while 循环执行
        while(a < 20) {
            println("Value of a: " + a);
            a += 1;
        }
    }
}
~~~

#### 2 do...while 循环
不像 while 循环在循环头部测试循环条件, Scala 语言中，do...while 循环是在循环的尾部检查它的条件。
do...while 循环与 while 循环类似，但是 do...while 循环会确保至少执行一次循环。

流程图
![](/images/scala/cpp_do_while_loop.jpg)

请注意，条件表达式出现在循环的尾部，所以循环中的 statement(s) 会在条件被测试之前至少执行一次。
如果条件为 true，控制流会跳转回上面的 do，然后重新执行循环中的 statement(s)。
这个过程会不断重复，直到给定条件变为 false 为止。

语法格式：

~~~scala
do  {
    statement(s);
} while (condition);
# ex
object Loops {
    def main(args: Array[String]) {
        // 局部变量
        var a = 10;
        // do 循环
        do{
            println("Value of a: " + a);
            a += 1;
        }while (a < 12);# 10
    }
}
~~~

#### 3 for 循环

语法格式：

~~~scala
for( var x <- Range ){
   statement(s);
}
# ex
object Loops {
    def main(args: Array[String]) {
        var a = 0;
        //for Loop
        for (a <- 1 to 10) {
            println("Value of a: " + a);
        }
    }
}
~~~

以下是一个使用了 i until j 语法(不包含 j)的实例:

~~~scala
object Loops {
    def main(args: Array[String]) {
        var a = 0;
        //for Loop
        for (a <- 1 until 10) {
            println("Value of a: " + a);
        }
    }
}
~~~

在 for 循环 中你可以使用分号 (;) 来设置多个区间，它将迭代给定区间所有的可能值。以下实例演示了两个区间的循环实例：

~~~scala
object Loops {
    def main(args: Array[String]) {
        var a = 0;
        var b = 0;
        //for Loop
        for (a <- 1 to 3;b <- 1 to 3) {
            println("Value of a: " + a);
            println("Value of b: " + b);

        }
    }
}
# output
Value of a: 1
Value of b: 1
Value of a: 1
Value of b: 2
Value of a: 1
Value of b: 3
Value of a: 2
Value of b: 1
Value of a: 2
Value of b: 2
Value of a: 2
Value of b: 3
Value of a: 3
Value of b: 1
Value of a: 3
Value of b: 2
Value of a: 3
Value of b: 3
~~~

for 循环集合

~~~scala
for( var x <- List ){
   statement(s);
}
# ex
object Loops {
    def main(args: Array[String]) {
        var a = 0;
        val numList = List(1,2,3,4,5,6)
        //for Loop
        for (a <- numList){
            println("Value of a:" +a);
        }
    }
}
~~~

for 循环过滤
Scala 可以使用一个或多个 if 语句来过滤一些元素。
以下是在 for 循环中使用过滤器的语法。

~~~scala
for( var x <- List
      if condition1; if condition2...
   ){
   statement(s);
# ex
object Loops {
    def main(args: Array[String]) {
        var a = 0;
        val numList = List(1,2,3,4,5,6,7,8,9);
        //for Loop
        for (a <- numList if a != 3; if a<8){
            println("Value of a: " +a);
        }
    }
}
~~~

for 使用 yield
你可以将 for 循环的返回值作为一个变量存储。语法格式如下：

~~~scala
var retVal = for{ var x <- List
     if condition1; if condition2...
}yield x
# ex
object Loops {
    def main(args: Array[String]) {
        var a = 0;
        val numList = List(1,2,3,4,5,6,7,8,9);
        //for Loop
        var retVal = for { a <- numList
                                    if a!= 3; if a < 8
                                    }yield a
        //输出返回值
        for (a <- retVal) {
            println("Value of a: " + a);
        }
    }
}
~~~


注意大括号中用于保存变量和条件，retVal 是变量， 循环中的 yield 会把当前的元素记下来，保存在集合中，循环结束后将返回该集合。

#### 4 循环控制语句

Scala 语言中默认是没有 break 语句，但是你在 Scala 2.8 版本后可以使用另外一种方式来实现 break 语句。当在循环中使用 break 语句，在执行到该语句时，就会中断循环并执行循环体之后的代码块。

Scala 中 break 的语法有点不大一样，格式如下：

~~~scala
// 导入以下包
import scala.util.control._

// 创建 Breaks 对象
val loop = new Breaks;

// 在 breakable 中循环
loop.breakable{
    // 循环
    for(...){
       ....
       // 循环中断
       loop.break;
   }
}
~~~

流程图
![](/images/scala/cpp_break_statement.jpg)

~~~
import scala.util.control._

object Loops {
    def main(args: Array[String]) {
        var a = 0;
        val numList = List(1,2,3,4,5,6,7,8,9);
        //Loop
        val loop = new Breaks;
        loop.breakable{
            for (a <- numList) {
                println("Value of a: " + a);
                if (a == 4) {
                    loop.break;
                }
            }
        }
        println("After th loop");
    }
}
# output
Value of a: 1
Value of a: 2
Value of a: 3
Value of a: 4
After the loop
~~~

中断嵌套循环
以下实例演示了如何中断嵌套循环：

~~~scala
import scala.util.control._

object Breaks {
    def main(args: Array[String]) {
        var a = 0;
        var b = 0;
        var numList1 = List(1,2,3,4,5);
        var numList2 = List(11, 12, 13);

        val outer = new Breaks;
        val inner = new Breaks;

        outer.breakable {
            for (a <- numList1) {
                println("Value of a: " + a);
                inner.breakable {
                    for (b <- numList2) {
                        println("Value of b: " + b);
                        if (b == 12) {
                            inner.break;
                        }
                    }
                }// Inner
            }
        }// Outer
    }
}
#output
Value of a: 1
Value of b: 11
Value of b: 12
Value of a: 2
Value of b: 11
Value of b: 12
Value of a: 3
Value of b: 11
Value of b: 12
Value of a: 4
Value of b: 11
Value of b: 12
Value of a: 5
Value of b: 11
Value of b: 12
~~~
