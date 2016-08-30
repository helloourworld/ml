---
layout: post
title: scala Lesson 2
description: Scala 访问修饰符，运算符
category: Language
catalog: yes
tags:
    - Scala
---
## Scala 访问修饰符
Scala 访问修饰符基本和Java的一样，分别有：private，protected，public。
如果没有指定访问修饰符符，默认情况下，Scala对象的访问级别都是 public。
Scala 中的 private 限定符，比 Java 更严格，在嵌套类情况下，外层类甚至不能访问被嵌套类的私有成员。

### 1 私有(Private)成员
用private关键字修饰，带有此标记的成员仅在包含了成员定义的类或对象内部可见，同样的规则还适用内部类。

~~~scala
class Outer{
    class Inner{
    private def f(){println("f")}
    class InnerMost{
        f() // 正确
        }
    }
    (new Inner).f() //错误
}
~~~
(new Inner).f( ) 访问不合法是因为 f 在 Inner 中被声明为 private，而访问不在类Inner之内。
但在 InnerMost 里访问f就没有问题的，因为这个访问包含在 Inner 类之内。
Java中允许这两种访问，因为它允许外部类访问内部类的私有成员。

### 2 保护(Protected)成员
在 scala 中，对保护（Protected）成员的访问比 java 更严格一些。因为它只允许保护成员在定义了该成员的的类的子类中被访问。而在java中，用protected关键字修饰的成员，除了定义了该成员的类的子类可以访问，同一个包里的其他类也可以进行访问。

~~~scala
package p{
class Super{
    protected def f() {println("f")}
    }
    class Sub extends Super{
        f()
    }
    class Other{
        (new Super).f() //错误
    }
}
~~~
上例中，Sub 类对 f 的访问没有问题，因为 f 在 Super 中被声明为 protected，而 Sub 是 Super 的子类。相反，Other 对 f 的访问不被允许，因为 other 没有继承自 Super。而后者在 java 里同样被认可，因为 Other 与 Sub 在同一包里。

### 3 公共(Public)成员
Scala中，如果没有指定任何的修饰符，则默认为 public。这样的成员在任何地方都可以被访问。
~~~scala
class Outer {
   class Inner {
      def f() { println("f") }
      class InnerMost {
         f() // 正确
      }
   }
   (new Inner).f() // 正确因为 f() 是 public
}
~~~
### 4 作用域保护
Scala中，访问修饰符可以通过使用限定词强调。格式为:

~~~scala
private[x]
或
protected[x]
~~~
这里的x指代某个所属的包、类或单例对象。如果写成private[x],读作"这个成员除了对[…]中的类或[…]中的包中的类及它们的伴生对像可见外，对其它所有类都是private。
这种技巧在横跨了若干包的大型项目中非常有用，它允许你定义一些在你项目的若干子包中可见但对于项目外部的客户却始终不可见的东西。

~~~scala
package bobsrocckets{
    package navigation{
        private[bobsrockets] class Navigator{
         protected[navigation] def useStarChart(){}
         class LegOfJourney{
             private[Navigator] val distance = 100
             }
            private[this] var speed = 200
            }
        }
        package launch{
        import navigation._
        object Vehicle{
        private[launch] val guide = new Navigator
        }
    }
}
~~~
上述例子中，类Navigator被标记为private[bobsrockets]就是说这个类对包含在bobsrockets包里的所有的类和对象可见。
比如说，从Vehicle对象里对Navigator的访问是被允许的，因为对象Vehicle包含在包launch中，而launch包在bobsrockets中，相反，所有在包bobsrockets之外的代码都不能访问类Navigator。

## 运算符

### 1 算术运算符

~~~scala
object Yunsunfu{
    def main(args: Array[String]){
        var a = 10;
        var b = 20;
        var c = 25;
        var d = 25;
        println("a+b=" + (a+b));
        println("a-b=" + (a-b));
        println("a*b=" + (a*b));
        println("b / a =" + (b / a));
        println("b % a = " + (b % a));
        println("c % a = " + (c % a));
    }
}
~~~

### 2 关系运算符

下表列出了 Scala 支持的关系运算符。
假定变量 A 为 10，B 为 20：

运算符| 描述|  实例
==|  等于  |(A == B) 运算结果为 false
!= | 不等于| (A != B) 运算结果为 true
>  | 大于|  (A > B) 运算结果为 false
<  | 小于|  (A < B) 运算结果为 true
>=|  大于等于|    (A >= B) 运算结果为 false
<=|  小于等于|    (A <= B) 运算结果为 true

~~~scala
object Test {
   def main(args: Array[String]) {
      var a = 10;
      var b = 20;
      println("a == b = " + (a == b) );
      println("a != b = " + (a != b) );
      println("a > b = " + (a > b) );
      println("a < b = " + (a < b) );
      println("b >= a = " + (b >= a) );
      println("b <= a = " + (b <= a) );
   }
}
~~~

### 3 逻辑运算符
下表列出了 Scala 支持的逻辑运算符。
假定变量 A 为 1，B 为 0：

运算符| 描述|  实例
&&|  逻辑与 (A && B)| 运算结果为 false
 \|\| |  逻辑或 (A \|\| B)| 运算结果为 true
!|   逻辑非 !(A && B)| 运算结果为 true

~~~scala
object Yunsunfu{
    def main(args: Array[String]){
        var a = true;
        var b = false;

        println("a && b = " + (a && b));
        println("a || b = " + (a || b));
        println("! (a && b) = " + (!(a && b)));
    }
}
~~~

### 4 位运算符
位运算符用来对二进制位进行操作，~,&,\|,^分别为取反，按位与与，按位与或，按位与异或运算，如下表实例：

p|   q|   p & q|   p \| q|   p ^ q
0 |  0  | 0  | 0  | 0
0 |  1  | 0  | 1  | 1
1 |  1  | 1  | 1  | 0
1 |  0  | 0  | 1  | 1

如果指定 A = 60; 及 B = 13; 两个变量对应的二进制为：

~~~scala
A = 0011 1100
B = 0000 1101
-------位运算----------

A&B = 0000 1100

A|B = 0011 1101

A^B = 0011 0001

~A  = 1100 0011

object Yunsunfu{
    def main(args: Array[String]){
        var a = 60;
        var b = 13;

        println("a & b = " + (a & b));
        println("a | b = " + (a | b));
        println("a ^ b = " + (a^b));
        println("~a = " + (~a));
        println("a << 2 =" + (a << 2));
        println("a \>\> 2 =" + (a \>\> 2));
        println("a \>\>> 2 =" + (a \>\>>2));
    }
}

output:
a & b = 12
a | b = 61
a ^ b = 49
~a = -61
a << 2 =240
a >> 2 =15
a >>> 2 =15
~~~
Scala 中的按位运算法则如下：

运算符 |   描述  |   实例
&   |   按位与运算符  |   (a & b) 输出结果 12 ，二进制解释： 0000 1100
 \|  |   按位或运算符  |   (a \| b) 输出结果 61 ，二进制解释： 0011 1101
^   |   按位异或运算符 |   (a ^ b) 输出结果 49 ，二进制解释： 0011 0001
~   |   按位取反运算符 |   (~a ) 输出结果 -61 ，二进制解释： 1100 0011， 在一个有符号二进制数的补码形式。
<<  |   左移动运算符  |   a << 2 输出结果 240 ，二进制解释： 1111 0000
\>\>  |   右移动运算符  |   a \>\> 2 输出结果 15 ，二进制解释： 0000 1111
\>\>\> |   无符号右移   |   a \>\>\>2 输出结果 15, 二进制解释: 0000 1111

### 5 赋值运算符
以下列出了 Scala 语言支持的赋值运算符:

运算符 |   描述  |   实例
=   |   简单的赋值运算，指定右边操作数赋值给左边的操作数。   |   C = A + B 将 A + B 的运算结果赋值给 C
+=  |   相加后再赋值，将左右两边的操作数相加后再赋值给左边的操作数。  |   C += A 相当于 C = C + A
-=  |   相减后再赋值，将左右两边的操作数相减后再赋值给左边的操作数。  |   C -= A 相当于 C = C - A
*=  |   相乘后再赋值，将左右两边的操作数相乘后再赋值给左边的操作数。  |   C *= A 相当于 C = C * A
/=  |   相除后再赋值，将左右两边的操作数相除后再赋值给左边的操作数。  |   C /= A 相当于 C = C / A
%=  |   求余后再赋值，将左右两边的操作数求余后再赋值给左边的操作数。  |   C %= A is equivalent to C = C % A
<<= |   按位左移后再赋值    |   C <<= 2 相当于 C = C << 2
\>\>= |   按位右移后再赋值    |   C \>\>= 2 相当于 C = C \>\> 2
&=  |   按位与运算后赋值    |   C &= 2 相当于 C = C & 2
^=  |   按位异或运算符后再赋值 |   C ^= 2 相当于 C = C ^ 2
 \|=  |   按位或运算后再赋值   |   C \|= 2 相当于 C = C \| 2

~~~scala
object Yunsunfu {
   def main(args: Array[String]) {
      var a = 10;
      var b = 20;
      var c = 0;

      c = a + b;
      println("c = a + b  =" + c );

      c += a
      println("c += a  =" + c);

      c -= a;
      println("c -= a  =" + c);

      c *= a;
      println("c *= a  =" + c);

      a = 10;
      c = 15;
      c /= a;
      println("c /= a  =" + c);

      a = 10;
      c = 15;
      c %= a;
      println("c %= a  =" + c);

      c <<= 2;
      println("c <<= 2  =" + c);

      c >>= 2;
      println("c >>=2  =" + c);

      c >>= 2;
      println("c >>=2  =" + c);

      c &= 2;
      println("c &= 2  =" +c);

      c ^= 2;
      println("c ^= 2  =" + c);

      c |= 2;
      println("c |= 2  =" + c);
    }
}
output:
c = a + b  =30
c += a  =40
c -= a  =30
c *= a  =300
c /= a  =1
c %= a  =5
c <<= 2  =20
c >>=2  =5
c >>=2  =1
c &= 2  =0
c ^= 2  =2
c |= 2  =2
~~~

### 6 运算符优先级

在一个表达式中可能包含多个有不同运算符连接起来的、具有不同数据类型的数据对象；由于表达式有多种运算，不同的运算顺序可能得出不同结果甚至出现错误运算错误，因为当表达式中含多种运算时，必须按一定顺序进行结合，才能保证运算的合理性和结果的正确性、唯一性。
优先级从上到下依次递减，最上面具有最高的优先级，逗号操作符具有最低的优先级。
相同优先级中，按结合顺序计算。大多数运算是从左至右计算，只有三个优先级是从右至左结合的，它们是单目运算符、条件运算符、赋值运算符。
基本的优先级需要记住：

* \* 指针最优，单目运算优于双目运算。如正负号。
* \* 先乘除（模），后加减。
* \* 先算术运算，后移位运算，最后位运算。请特别注意：1 << 3 + 2 & 7 等价于 (1 << (3 + 2))&7
* \* 逻辑运算最后计算

