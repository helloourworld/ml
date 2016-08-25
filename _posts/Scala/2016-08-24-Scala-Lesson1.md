---
layout: post
title: scala Lesson 1
description: 变量函数，操作符，基本类型
category: blog
catalog: yes
tags:
    - Scala
---
## 变量函数

### 1 变量：var，val，----immutable

>* Scala的变量分两种，var和val。var，即variable，类似于我们在Java等其他语言中接触到的变量，而val，是value，类似于我们在其他语言中用到的不可重新赋值的常量，或者final变量。Scala非常强调不可变（immutable）的概念。
>* Scala编译器通过类型推断（Type Inference）可推断出数据类型，也可显式指定变量类型，类型在变量名称后，用冒号（:）分隔。

~~~scala
var: a: Int = 5
~~~

### 2 函数（function），过程（procedure）

>* 函数定义以def开始，然后是函数名称，接下来，小括号内是函数的参数列表，参数之间逗号分隔。与Java或C不同的是，参数的类型出现在参数名之后，与参数名称冒号分隔。 函数的类型（也就是返回值的类型）在参数列表之后，也用冒号分隔。在函数类型之后，是等号“=”，然后才是大括号包围起来的函数体。函数返回类型可以省略，因为编译器可以推断出来。不过，为了代码的可读性，应该尽量注明返回类型，只有在代码非常简短，能一眼看出返回类型的情况下，可省略它。

~~~
def add(x: Int, y: Int): Int = {
    x + y
}
println("2 + 3 = " + add(2, 3))
~~~
>* 过程的目的是为了某种“副作用”，而不是为了得到计算结果。如上所述，过程只是一种特殊的函数，具体来说，是没有返回值，或者说返回类型为Unit的函数。

~~~
def sayHiTo(name: String) {
println("Hi, " + name)
}
sayHiTo("DAVE")
~~~

PS:

(1) 命名参数可让你在时指定参数名，这样，参数的位置将不再重要。

(2) 默认参数，同其它语言。

(3) 重复参数，类型后面有一个'*'，这表示这个参数可以重复不定次数，包括0次。当参数个数不是0时，重复参数在内部其实是一个Array。使用时跟Array差不多，只是，调用时不能传递直接Array进来。如果需要传递整个Array（或者别的类型的序列）的话，有一个变通方法，那就是，加一个'_*'符号，该符号与参数之间用逗号分隔，比如log(array: _*)。

~~~
def log(msgs: String*) = {
  println(msgs.getClass.getName)
  println(msgs.mkString(","))
}

log()
log("one","two","three")
val array = Array("one","two","three")
//log(array)
//above line wouldn't compile, type mismatch, expected String
log(array: _*)
~~~

### 操作符

>* Scala没有操作符，也没有通常意义上的表达式。其实是方法（函数），叫做操作符记法。如 1+2 与 1.＋（2）
>* 能做前缀的操作符很少，只有四个：+，-，!，~。另外，前缀表达式有一些特别，它们的方法需要把对应的操作符前加上unary_>* ，比如unary_!，或者unary_-。
>* 由于操作符本质上就是方法，你可以跟方法重载一样重载操作符。
>* 常用操作符及优先性类似c++，不详述了。
>* * / %　　+ -　　:　　= !　　< >　　&　　^　　|

### 基本类型

在Scala中，基本类型也是class，比如，Int类型，来自scala.Int，每一个数字，都是scala.Int的一个实例。装箱（boxing）和拆箱（unboxing）操作是透明的，程序员不需要关心（实际上，这是由定义在Predef中的隐式转换完成的）。

* 1 数值类型

> * Byte: 8位, 有符号（2<sup>-7</sup> ~ 2<sup>7</sup> - 1)
> * Short: 16位, 有符号 （2<sup>-15</sup> ~ 2<sup>15</sup> - 1)
> * Int: 32位, 有符号 （2<sup>-31</sup> ~ 2<sup>31</sup> - 1)
> * Long: 64位, 有符号 （2<sup>-63</sup> ~ 2<sup>63</sup> - 1)
> * Char: 16位, 无符号 （0 ~ 2<sup>16</sup> - 1)
> * Float: 32位, 单精度浮点数
> * Double: 64位, 双精度浮点数
>* 以`:`结尾的操作符是右结合的，其他操作符都是左结合的。

每一个基本类型都有一个相对应的富包装类。 基本类型，在必要的时候通过隐式转换转换为对应的富包装类，从而可调用富包装类提供的方法。

~~~ ruby
／／RichInt
val n1 = 2 max 3
println("2 max 3 = " + n1)

val n2 = -1.abs
println("-1.abs = " + n2)

val n3 = 1 to 5
println("1 to 5 = " + n3)

val n4 = 1.isValidChar
println("1.isValidChar = " + n4)

val n5 = -1.isValidChar
println("-1.isValidChar = " + n5)
~~~

* 2 String

Scala里的String是直接借用了Java的String。不过，由于String实际是一系列Char的不可变的集合，Scala中大部分针对集合的操作，都可以用于String，具体来说，String的这些方法存在于类scala.collection.immutable.StringOps中。 由于String在需要时能隐式转换为StringOps，因此不需要任何额外的转换，String就可以使用这些方法。

```
val r3 = str.filter( _ != 'l') //"Heo"
println("\"Hello\".filter( _ != 'l') = " + r3)
```
与Java或C#一样，String是不可变的，对String进行操作，会得到新的String实例。因此在需要频繁操作String的情况下，请使用StringBuilder。

~~~
val builder = new StringBuilder
builder.append("Hello")
builder.append(", world")
builder += '!'
builder.insert(0,"Me: ")
println(builder)   //Me: Hello, world!
~~~

* 3 字面常量：同java。XML扩展。函数常量。

~~~
val fun=
new Function2[Int, Int, Int] {
    def apply(x: Int, y: Int): Int = x + y
}
val result = fun(2,4)
println("Result = " + result)
~~~

* 4 类对象

Scala中，所有的值都是类对象，而所有的类，包括值类型，都最终继承自一个统一的根类型Any。统一类型，是Scala的又一大特点。更特别的是，Scala中还定义了几个底层类（Bottom Class），比如Null和Nothing。

![](/images/scala/anyclass.png)

* Null是所有引用类型的子类型，而Nothing是所有类型的子类型。Null类只有一个实例对象，null，类似于Java中的null引用。null可以赋值给任意引用类型，但是不能赋值给值类型。
* Nothing，可以作为没有正常返回值的方法的返回类型，非常直观的告诉你这个方法不会正常返回，而且由于Nothing是其他任意类型的子类，他还能跟要求返回值的方法兼容。
* Unit类型用来标识过程，也就是没有明确返回值的函数。 由此可见，Unit类似于Java里的void。Unit只有一个实例，()，这个实例也没有实质的意义。
