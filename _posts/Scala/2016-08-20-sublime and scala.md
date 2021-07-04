---
layout: post
title: scala with sublime
description: compile and execute with sublime.
category: Language
catalog: yes
tags:
    - Scala
    - IT
---
## Scala特点：

> * Scala是一种纯面向对象的语言，一切都是对象：将原始类型和类统一起来，同时也将函数和操作符统一起来。
> * Scala又是函数式语言，这体现在，函数在Scala中也是一种对象，并且能非常自然的使用高阶函数。
> * Scala是静态类型的语言，但是由于它强大的类型推断，实际需要指定类型的地方并不多。在拥有静态语言和编译型语言的安全高效的优势的同时，Scala使用起来像Ruby，Python等静态语言一样方便，灵活，简洁。

## sublime IDE 配置
 【Tools】->【Build System】->【New Build System】
输入如下内容：

~~~sublime
{
    "cmd": ["scalac", "-d", "%tmp%", "$file", "&", "scala", "-cp", "%tmp%", "$file_base_name"],
    "selector": ["source.scala"],
    "shell": "true"
    "encoding": "cp936"
}
~~~

保存。然后就可以使用ctrl + b 编译与执行scala脚本了。

ps: 使用同样的配置也能够执行java的编译与执行。方法：

> 将cmdjava替换为如下bat脚本，并放置于javac路径

~~~bat
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
~~~

## scala示例

~~~scala
object Hello {
  def main(args: Array[String]): Unit = {
    println("Hello, world!")
  }
}
~~~

## R 示例

~~~ R
{"cmd": ["Rscript.exe", "$file"],
"path": "C:\\Program Files\\R\\R-3.3.0\\bin\\x64\\",
"selector": "source.r"}
~~~

## JavaScript 示例
~~~ JavaScript
{
  "cmd": ["taskkill","/F", "/IM", "node.exe","&","node", "$file"],
  "cmd": ["node", "$file"],
  "file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
  "selector": "source.js",
  "shell":true,
  "encoding": "cp936"
}
~~~


## Decode error解决

出现：[Decode error - output not utf-8]

解决：
发现是print不支持中文字符的输出， 需要修改build的setting， 打开.sublime-build,
添加：

~~~sublime
{
     "encoding": "cp936"
}
~~~

## Sublime text 2/3 [Decode error - output not utf-8] 完美解决方法

1.在sublime text的安装目录下的Packages\目录下找到Default.sublime-package,将这个复制出来,将后缀改名为zip.

2.打开exec.py.找到类ExecCommand的append_data函数,在以下位置添加代码

~~~
    def append_data(self, proc, data):
         if proc != self.proc:
             # a second call to exec has been made before the first one
             # finished, ignore it instead of intermingling the output.
             if proc:
                 proc.kill()
             return

         #add start
         is_decode_ok = True;
         try:
             str = data.decode(self.encoding)
         except:
             is_decode_ok = False
         if is_decode_ok==False:
             try:
                 str = data.decode("gbk")
             except:
                 str = "[Decode error - output not " + self.encoding + " and gbk]\n"
                 proc = None

         # Normalize newlines, Sublime Text always uses a single \n separator
         # in memory.
         str = str.replace('\r\n', '\n').replace('\r', '\n')

         self.output_view.run_command('append', {'characters': str, 'force': True, 'scroll_to_end': True})
~~~
