---
layout: post
title: scala with sublime
description: compile and execute with sublime.
category: blog
catalog: yes
tags:
    - IT
    - Scala
---
## Scala特点：

> * Scala是一种纯面向对象的语言，一切都是对象：将原始类型和类统一起来，同时也将函数和操作符统一起来。
> * Scala又是函数式语言，这体现在，函数在Scala中也是一种对象，并且能非常自然的使用高阶函数。
> * Scala是静态类型的语言，但是由于它强大的类型推断，实际需要指定类型的地方并不多。在拥有静态语言和编译型语言的安全高效的优势的同时，Scala使用起来像Ruby，Python等静态语言一样方便，灵活，简洁。

## sublime IDE 配置
 【Tools】->【Build System】->【New Build System】
输入如下内容：

    `
{
    "cmd": ["scalac", "-d", "%tmp%", "$file", "&", "scala", "-cp", "%tmp%", "$file_base_name"],
    "selector": ["source.scala"],
    "shell": "true"
}
    `

保存。然后就可以使用ctrl + b 编译与执行scala脚本了。

ps: 使用同样的配置也能够执行java的编译与执行。方法：

> 将cmdjava替换

    `
    @ECHO OFF
cd %~dp1
ECHO Compiling %~nx1......
IF EXIST %~n1.class (
    DEL %~n1.class
    )
javac %~nx1
IF EXIST %~n1.class (
    ECHO ----------------------OUTPUT--------------
    java %~n1
    )
    `

## scala示例

    ·
object Hello {
  def main(args: Array[String]): Unit = {
    println("Hello, world!")
  }
}
    ·
