---
layout: post
title: scala Lesson 3
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

#### 3 函数调用

