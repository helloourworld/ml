---
layout: post
title: 培训虚拟机使用指南
category: Hadoop
catalog: yes
description: 使用指南
tags:
    - Big Data
    - 大数据
---
##   开启虚拟机

确保安装了VMware软件。打开虚拟机后查看IP并更改hosts。注意：请根据本地的IP来更改，查看本地ip方法：ifconfig命令查看。默认NN01为~128；DN01为129；DN02为130。

~~~
Last login: Tue Aug 23 18:47:50 2016 from 192.168.71.1
[hadoop@NN01 ~]$ sudo vim /etc/hosts
[hadoop@NN01 ~]$ sudo tools/deploy.sh /etc/hosts /etc/hosts slave
****DN01.HadoopVM*****
Warning: Permanently added the RSA host key for IP address '192.168.71.134' to the list of known hosts.
hosts                                 100%  293     0.3KB/s   00:00
****DN02.HadoopVM*****
Warning: Permanently added the RSA host key for IP address '192.168.71.133' to the list of known hosts.
hosts                                 100%  293     0.3KB/s   00:00
[hadoop@NN01 ~]$
~~~

## something

