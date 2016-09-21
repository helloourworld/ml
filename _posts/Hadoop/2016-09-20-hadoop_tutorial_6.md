---
layout: post
title: Spark安装 快速指南
category: Hadoop
catalog: yes
description: Spark 安装
tags:
    - Big Data
    - 大数据
---

## Prerequisites

3台虚拟机
SSH免密登陆
Java已安装
tools工具已经下载

## Install Spark
Step 1: Install Pip
pip 是“A tool for installing and managing Python packages.”，也就是说pip是python的软件安装工具。使用root身份

~~~
cd /usr/local/src
wget "https://pypi.python.org/packages/source/p/pip/pip-1.5.4.tar.gz#md5=834b2904f92d46aaa333267fb1c922bb" --no-check-certificate
tar -xzvf pip-1.5.4.tar.gz
cd pip-1.5.4
python setup.py install
pip --version
pip 1.5.4 from /usr/lib/python2.6/site-packages/pip-1.5.4-py2.6.egg (python 2.6)
~~~

Step 2: Install Scala

~~~scala
$ cd ~/Downloads
 $ wget http://www.scala-lang.org/files/archive/scala-2.11.8.deb
 $ sudo dpkg -i scala-2.11.8.deb
 $ scala –version
~~~

Step 3: Install py4j

Py4J is used on the driver for local communication between the Python and Java SparkContext objects; large data transfers are performed through a different mechanism.

~~~
 $ sudo pip install py4j
~~~

Step 4: Install Spark.

By now, we have installed the dependencies which are required to install Apache Spark. Next, we need to download and extract Spark source tar. We can get the latest version Apache Spark using wget:

~~~
 $ cd ~/Downloads
 $ wget http://d3kbcqa49mib13.cloudfront.net/spark-1.6.0.tgz
 $ tar xvf spark-1.6.0.tgz
~~~

Step 5: Compile the extracted source

 sbt is an open source build tool for Scala and Java projects which is similar to Java’s Maven.

~~~
 $ cd ~/Downloads/spark-1.6.0
 $ sbt/sbt assembly
~~~

This will take some time to install Spark. After installing, we can check whether Spark is running correctly or not by typing.

~~~
 $ ./bin/run-example SparkPi 10
~~~

this will produce the output:

Pi is roughly 3.14042

To see the above results we need to lower the verbosity level of the log4j logger in log4j.properties.

~~~
$ cp conf/log4j.properties.template conf/log4j.properties
$ nano conf/log4j.properties
~~~
After opening the file ‘log4j.properties’, we need to replace following line:

~~~
log4j.rootCategory=INFO, console
~~~

by

~~~
log4j.rootCategory=ERROR, console
~~~

Step 6: Move the files in the right folders (to make it convenient to access them)

~~~
$ sudo mv ~/Downloads/spark-1.6.0 /opt/
$ sudo ln -s /opt/spark-1.6.0 /opt/spark
~~~

Add this to your path by editing your bashrc file:

Step 7: Create environment variables. To set the environment variables, open bashrc file in any editor.

~~~
 $ nano ~/.bashrc
~~~

Set the SPARK_HOME and PYTHONPATH by adding following lines at the bottom of this file

~~~
export SPARK_HOME=/opt/spark
export PYTHONPATH=$SPARK_HOME/python
~~~

Next, restart bashrc by typing in:

~~~
 $ . ~/.bashrc
~~~

Let’s add  this setting for ipython by creating a new python script to automatically export settings, just in case above change did not work.

~~~
$ nano ~/.ipython/profile_default/startup/load_spark_environment_variables.py
~~~

Paste some lines in this file.

~~~
import os
import sys

if 'SPARK_HOME' not in os.environ:
    os.environ['SPARK_HOME'] = '/opt/spark'

if '/opt/spark/python' not in sys.path:
    sys.path.insert(0, '/opt/spark/python')
~~~


Step 8: We are all set now. Let us start PySpark by typing command in root directory:

~~~
 $ ./bin/pyspark --packages
~~~
We can also start ipython notebook in shell by typing:

~~~
 $ PYSPARK_DRIVER_PYTHON=ipython ./bin/pyspark
~~~

When we launch the shell in PySpark, it will automatically load spark Context as sc and SQLContext as sqlContext.

## Windows轻松安装Spark

[https://msdn.microsoft.com/zh-cn/magazine/mt595756.aspx](https://msdn.microsoft.com/zh-cn/magazine/mt595756.aspx)
