---
layout: post
title: scala Lesson 4
description: Scala 函数
category: Language
catalog: yes
tags:
    - Scala
---
### 函数

函数是一组一起执行一个任务的语句。 您可以把代码划分到不同的函数中。如何划分代码到不同的函数中是由您来决定的，但在逻辑上，划分通常是根据每个函数执行一个特定的任务来进行的。
Scala 有函数和方法，二者在语义上的区别很小。Scala 方法是类的一部分，而函数是一个对象可以赋值给一个变量。换句话来说在类中定义的函数即是方法。
我们可以在任何地方定义函数，甚至可以在函数内定义函数（内嵌函数）。更重要的一点是 Scala 函数名可以由以下特殊字符：+, ++, ~, &,-, -- , \, /, : 等。

#### 1 函数声明

语法格式：

~~~scala
def functionName ([参数列表]) : [return type]
~~~
如果你不写等于号和方法主体，那么方法会被隐式声明为"抽象(abstract)"，包含它的类型于是也是一个抽象类型。

#### 2 函数定义
方法定义由一个def 关键字开始，紧接着是可选的参数列表，一个冒号"：" 和方法的返回类型，一个等于号"="，最后是方法的主体。
Scala 函数定义格式如下：

~~~scala
def functionName ([参数列表]) : [return type] = {
   function body
   return [expr]
}
~~~
以上代码中 return type 可以是任意合法的 Scala 数据类型。参数列表中的参数可以使用逗号分隔。
以下函数的功能是将两个传入的参数相加并求和：

~~~scala
object Funadd{
   def addInt( a:Int, b:Int ) : Int = {
      var sum:Int = 0
      sum = a + b

      return sum
   }
   def main(args: Array[String]){
      println("value of 1 add 2: " + addInt(1,2));
   }
}
~~~

如果函数没有返回值，可以返回为 **Unit**，这个类似于 **Java** 的 void, 实例如下：

~~~scala
object Hello{
   def printMe( ) : Unit = {
      println("Hello, Scala!")
   }
}
~~~

#### 3 函数调用

Scala 提供了多种不同的函数调用方式：
以下是调用方法的标准格式：

~~~scala
functionName( 参数列表)
~~~

如果函数使用了实例的对象来调用，我们可以使用类似java的格式 (使用 . 号)：

~~~scala
[instance.]functionName( 参数列表 )
~~~

实例演示了定义与调用函数的实例:

~~~scala
object Test {
   def main(args: Array[String]) {
        println( "Returned Value : " + addInt(5,7) );
   }
   def addInt( a:Int, b:Int ) : Int = {
      var sum:Int = 0
      sum = a + b

      return sum
   }
}
~~~

Scala也是一种函数式语言，所以函数是 Scala 语言的核心。以下一些函数概念有助于我们更好的理解 Scala 编程：

#### 4 函数传名调用(call-by-name)

Scala的解释器在解析函数参数(function arguments)时有两种方式：

* \* 传值调用（call-by-value）：先计算参数表达式的值，再应用到函数内部；
* \* 传名调用（call-by-name）：将未计算的参数表达式直接应用到函数内部

在进入函数内部前，传值调用方式就已经将参数表达式的值计算完毕，而传名调用是在函数内部进行参数表达式的值计算的。
这就造成了一种现象，每次使用传名调用时，解释器都会计算一次表达式的值。

~~~scala
object Funcallbys {
   def main(args: Array[String]) {
        delayed(time());
   }

   def time() = {
      println("获取时间，单位为纳秒")
      System.nanoTime
   }
   def delayed( t: => Long ) = {
      println("在 delayed 方法内")
      println("参数： " + t)
      t
   }
}
~~~

以上实例中我们声明了 delayed 方法， 该方法在变量名和变量类型使用 **=> 符号**来设置传名调用。执行以上代码，输出结果如下：output:

~~~scala
在 delayed 方法内
获取时间，单位为纳秒
参数： 91298184175656
获取时间，单位为纳秒
~~~
实例中 delay 方法打印了一条信息表示进入了该方法，接着 delay 方法打印接收到的值，最后再返回 t。


#### 5 指定函数参数名

一般情况下函数调用参数，就按照函数定义时的参数顺序一个个传递。但是我们也可以通过指定函数参数名，并且不需要按照顺序向函数传递参数，实例如下：

~~~scala
object Funcallbys {
   def main(args: Array[String]) {
        printInt(b=5, a=7);
   }
   def printInt( a:Int, b:Int ) = {
      println("Value of a : " + a );
      println("Value of b : " + b );
   }
}
~~~

#### 6 函数 - 可变参数

Scala 允许你指明函数的最后一个参数可以是重复的，即我们不需要指定函数参数的个数，可以向函数传入可变长度参数列表。
Scala 通过在参数的类型之后放一个星号来设置可变参数(可重复的参数)。例如：

~~~scala
object Funcallbys{
   def main (args: Array[String]) {
      dosome("1", "2", "3", "4");
   }
   def dosome(args: String*) = {
      var i: Int = 0;
      for (arg <- args) {
         println("Arg value [" + i + "]" + arg);
         i += 1;
      }
   }
}
~~~

#### 7 递归函数

递归函数在函数式编程的语言中起着重要的作用。
Scala 同样支持递归函数。
递归函数意味着函数可以调用它本身。
以上实例使用递归函数来计算阶乘：

~~~scala
object Factorial {
    def main(args: Array[String]) {
        for (i <- 1 to 9)
        println("factorial" + i + "is: " + factorial(i));
    }
    def factorial(n: BigInt): BigInt = {
        if (n <= 1)
            1
        else
            n * factorial(n-1)
    }
}

~~~

#### 8 函数 - 默认参数值

Scala 可以为函数参数指定默认参数值，使用了默认参数，你在调用函数的过程中可以不需要传递参数，这时函数就会调用它的默认参数值，如果传递了参数，则传递值会取代默认值。实例如下：

~~~scala
object Fundefault{
    def main(args: Array[String]){
        println("return value: " + addInt());
    }

    def addInt(a: Int = 5, b: Int = 7): Int = {
        var sum: Int = 0
        sum = a + b
        return sum
    }
}
~~~

#### 9 高阶函数

高阶函数（Higher-Order Function）就是操作其他函数的函数。
Scala 中允许使用高阶函数, 高阶函数可以使用其他函数作为参数，或者使用函数作为输出结果。
以下实例中，apply() 函数使用了另外一个函数 f 和 值 v 作为参数，而函数 f 又调用了参数 v：

~~~scala
object Funapply {
   def main(args: Array[String]) {

      println( apply( layout, 10) )

   }
   // 函数 f 和 值 v 作为参数，而函数 f 又调用了参数 v
   def apply(f: Int => String, v: Int) = f(v)

   def layout[A](x: A) = "[" + x.toString() + "]"

}

# output
[10]
~~~

#### 10 函数嵌套

我么可以在 Scala 函数内定义函数，定义在函数内的函数称之为局部函数。
以下实例我们实现阶乘运算，并使用内嵌函数：

~~~scala
object FunInner {
   def main(args: Array[String]) {
      println( factorial(0) )
      println( factorial(1) )
      println( factorial(2) )
      println( factorial(3) )
   }

   def factorial(i: Int): Int = {
      def fact(i: Int, accumulator: Int): Int = {
         if (i <= 1)
            accumulator
         else
            fact(i - 1, i * accumulator)
      }
      fact(i, 1)
   }
}
~~~

#### 11 匿名函数

Scala 中定义匿名函数的语法很简单，箭头左边是参数列表，右边是函数体，参数的类型是可省略的，Scala 的类型推测系统会推测出参数的类型。使用匿名函数后，我们的代码变得更简洁了。
下面的表达式就定义了一个接受一个Int类型输入参数的匿名函数:

~~~scala
var inc = (x: Int) => x+1
~~~

上述定义的匿名函数，其实是下面这种写法的简写：

~~~scala
def add2 = new Function1[Int, Int]{
   def apply(x: Int): Int = x+1;
}
~~~

以上实例的inc现在可可作为一个函数，使用方法如下：

~~~scala
var x = inc(7) -1
~~~

同样我们可以在匿名函数中定义多个参数：

~~~scala
var mul = (x: Int, y: Int) => x*y
~~~

mul现在可作为一个函数，使用方法如下：

~~~scala
println(mul(3,4))
~~~

我们也可以不给匿名函数设置参数，如下所示：

~~~scala
var userDir = () => { System.getProperty("user.dir") }
~~~

userDir 现在可作为一个函数，使用方式如下：

~~~scala
println( userDir )
~~~

~~~scala
object Lambda{
   def main(args: Array[String]) {
      println("The user dir is: " + userDir());
   }

   var userDir = () => { System.getProperty("user.dir")}
}
~~~

#### 12 偏应用函数

Scala 偏应用函数是一种表达式，你不需要提供函数需要的所有参数，只需要提供部分，或不提供所需参数。
如下实例，我们打印日志信息：

~~~scala
import java.util.Date

object PartiallyAppliedFunction{
   def main(args: Array[String]){
      val date = new Date
      log(date, "message1")
      Thread.sleep(1000)
      log(date, "message2")
      Thread.sleep(1000)
      log(date, "message3")
   }

   def log(date: Date, message: String) = {
      println(date + "------" + message)
   }
}
~~~

实例中，log() 方法接收两个参数：date 和 message。我们在程序执行时调用了三次，参数 date 值都相同，message 不同。
我们可以使用偏应用函数优化以上方法，绑定第一个 date 参数，第二个参数使用下划线(_)替换缺失的参数列表，并把这个新的函数值的索引的赋给变量。以上实例修改如下：

~~~scala
import java.util.Date

object PartiallyAppliedFunction{
   def main(args: Array[String]){
      val date = new Date
      val logWithDateBonud = log(date, _ : String)

      logWithDateBonud("message1")
      Thread.sleep(1000)
      logWithDateBonud("message2")
      Thread.sleep(1000)
      logWithDateBonud("message3")
   }

   def log(date: Date, message: String) = {
      println(date + "------" + message)
   }
}
~~~

#### 13 Scala 函数柯里化(Currying)

类似python中，参考[]()。

柯里化(Currying)指的是将原来接受两个参数的函数变成新的接受一个参数的函数的过程。新的函数返回一个以原有第二个参数为参数的函数。

**实例**

首先我们定义一个函数:

`def add(x: Int, y: Int) = x+y`

那么我们应用的时候，应该是这样用：add(1,2)
现在我们把这个函数变一下形：

`def add(x: Int)(y: Int) = x + y`

那么我们在用的时候，应该是这样用：add(1)(2)

**实现过程**

add(1)(2)实际上是依次调用两个普通函数（非柯里化函数)，第一次调用使用一个参数 x，返回一个函数类型的值，第二次使用参数y调用这个函数类型的值。
实质上最先演变成这样一个方法：

`def add(x: Int) = (y: Int) => x+y`

那么这个函数是什么意思呢？ 接收一个x为参数，返回一个匿名函数，该匿名函数的定义是：接收一个Int型参数y，函数体为x+y。现在我们来对这个方法进行调用。

`val result = add(1)`

返回一个result，那result的值应该是一个匿名函数：(y:Int)=>1+y
所以为了得到结果，我们继续调用result。

`val sum = result(2)`

最后打印出来的结果就是3。

**完整实例**

~~~scala
object CurryFun{
   def main(args: Array[String]) {
      val str1: String = "Hello, "
      val str2: String = "Scala!"
      println("Str1 + Str2 = " + strcat(str1)(str2));
   }

   def strcat(s1: String)(s2: String) = {
      s1 + s2
   }
}
~~~

#### 14 Scala 闭包

闭包是一个函数，返回值依赖于声明在函数外部的一个或多个变量。

闭包通常来讲可以简单的认为是可以访问一个函数里面局部变量的另外一个函数。
如下面这段匿名的函数：

`val multiplier = (i:Int) => i * 10  `

函数体内有一个变量 i，它作为函数的一个参数。如下面的另一段代码：

`val multiplier = (i:Int) => i * factor`

在 multiplier 中有两个变量：i 和 factor。其中的一个 i 是函数的形式参数，在 multiplier 函数被调用时，i 被赋予一个新的值。然而，factor不是形式参数，而是自由变量，考虑下面代码：

~~~scala
var factor = 3
val multiplier = (i:Int) => i * factor
~~~

这里我们引入一个自由变量 factor，这个变量定义在函数外面。

这样定义的函数变量 multiplier 成为一个"闭包"，因为它引用到函数外面定义的变量，定义这个函数的过程是将这个自由变量捕获而构成一个封闭的函数。

**完整实例**

~~~scala
object Closures{
    def main(args: Array[String]){
        println("muliplier(1) value = " + multiplier(1))
        println("muliplier(2) value = " + multiplier(2))
    }

    var factor = 3
    val multiplier = (i: Int) => i * factor
}
~~~
