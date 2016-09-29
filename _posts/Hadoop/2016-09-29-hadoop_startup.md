---
layout: post
title: Hadoop 开机自启动
category: Hadoop
catalog: yes
description: 设置Hadoop程序开机自启动的方法
tags:
    - Big Data
    - 大数据
---

## 1 把启动程序的命令添加到/etc/rc.d/rc.local

加入后的文件如下：

~~~
#!/bin/sh
#
# This script will be executed *after* all the other init scripts.
# You can put your own initialization stuff in here if you don't
# want to do the full Sys V style init stuff.

touch /var/lock/subsys/local
# start hadoop

/home/hadoop/sbin/start-all.sh
/home/hadoop/sbin/mr-jobhistory-daemon.sh start historyserver --config $HADOOP_CONF_DIR

# start kafka
/home/hadoop/zookeeper.sh start

# start hiveserver2
hiveserver2
~~~

## 2 [chkconfig](https://www.centos.bz/2011/07/7-linux-chkconfig-command-examples/)设置开机启动。

[chkconfig](http://man.linuxde.net/chkconfig) 功能说明：检查，设置系统和各种服务。

讲法：chkconfig [--add][--del][--list][系统服务]或chkconfig [--level <等级代号>[系统服务][on/off/reset]

* --add：增加所指定的系统服务，让chkconfig指令得以管理它，并同时在系统启动的叙述文件内增加相关数据；
* --del：删除所指定的系统服务，不再由chkconfig指令管理，并同时在系统启动的叙述文件内删除相关数据；
* --level<等级代号>：指定读系统服务要在哪一个执行等级中开启或关毕。

等级代号列表

* 等级0表示：表示关机 等级1表示：单用户模式
* 等级2表示：无网络连接的多用户命令行模式
* 等级3表示：有网络连接的多用户命令行模式
* 等级4表示：不可用
* 等级5表示：带图形界面的多用户模式
* 等级6表示：重新启动

如何增加一个服务：

* 1 服务脚本必须存放在/etc/ini.d/目录下；
* 2 chkconfig --add servicename在chkconfig工具服务列表中增加此服务，此时服务会被在/etc/rc.d/rcN.d中赋予K/S入口了；
* 3 chkconfig --level 35 mysqld on修改服务的默认启动等级。

来自: http://man.linuxde.net/chkconfig
设置自启动mysql

~~~
# mysqld 放入linux启动管理体系中
[hadoop@NN01 init.d]$ sudo chkconfig --add mysqld
# 查看全部服务在各运行级状态
[hadoop@NN01 init.d]$ sudo chkconfig --list mysqld
mysqld          0:off   1:off   2:off   3:off   4:off   5:off   6:off
# 只有级别3启动
[hadoop@NN01 init.d]$ sudo chkconfig --levels 3 mysqld on
[hadoop@NN01 init.d]$ sudo chkconfig --list mysqld
mysqld          0:off   1:off   2:off   3:on    4:off   5:off   6:off
~~~

## 3 把启动程序的命令添加到/etc/rc.d/rc.sysint文件中

脚本/etc/rc.d/rc.sysint，完成系统服务程序启动，如系统环境变量设置、设置系统时钟、加载字体、检查加载文件系统、生成系统启动信息日志文件等。

比如设置自启动apache:

~~~
echo "/usr.local/appache2/bin/appachect1 start" >> /etc/rc.d/rc.sysint
~~~
