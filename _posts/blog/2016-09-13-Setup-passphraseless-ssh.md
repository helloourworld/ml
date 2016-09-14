---
layout: post
title: Setup passphraseless ssh
description: 配置免密码登陆
category: blog
catalog: yes
tags:
    - IT
---

## 配置免密码登陆：

### 1、生成公钥


~~~shell
[hadoop@NN01 ~]$ ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
Generating public/private rsa key pair.
Your identification has been saved in /home/hadoop/.ssh/id_rsa.
Your public key has been saved in /home/hadoop/.ssh/id_rsa.pub.
The key fingerprint is:
4b:cd:39:6d:79:e2:eb:7b:63:59:48:7a:1a:7d:41:5e hadoop@NN01.HadoopVM
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|               .E|
|              o .|
|         o o ..o |
|        S = =+...|
|       . . +oo+ o|
|        .   .+ + |
|            ..=  |
|           .++ . |
+-----------------+
[hadoop@NN01 ~]$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
[hadoop@NN01 ~]$  chmod 0600 ~/.ssh/authorized_keys
~~~

### 2、分发公钥

公钥目前只有本地localhost的：

~~~shell
[hadoop@NN01 .ssh]$ cat authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAz7rbB/msVLOPHuUEFmowjM2P+Y5wkn0qBm2wiVZRhfW+vaa5HPaR8BTSx+iZQvoDnG55VjFesaQvW3x5rhtV4tQGq9vtSThh7pHifd8EonBAe+Lv+5wqUqmpt/AIb0/94tKhPkEO4LLUePWeCT37omDGtos7lANidtwEyAhpUNtobMTSURnGoFS++ihvaAEwHFXo3UUtkI7+FFJkk/dMhjUMskCyIeKfIoLwOX0FzyrZarOlk/I+P2FH+sl/19nNFvuraJeMtq0KCD/i85bbty2juSRnOOOxr+xjgPNogL6fzU3VC2D1jpRGaqfXDQjXfVLMWv18O61ZKCvVak+GXQ== hadoop@NN01.HadoopVM
~~~

在DN01和DN02上进行分发：

~~~shell
[hadoop@DN01 .ssh]$ ssh-copy-id -i ~/.ssh/id_rsa.pub hadoop@NN01.HadoopVM
The authenticity of host 'nn01.hadoopvm (192.168.71.128)' can't be established.
RSA key fingerprint is 88:22:dc:4b:e4:4b:0b:55:e4:a0:48:03:0d:55:63:7e.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'nn01.hadoopvm,192.168.71.128' (RSA) to the list of known hosts.
hadoop@nn01.hadoopvm's password:
Now try logging into the machine, with "ssh 'hadoop@NN01.HadoopVM'", and check in:

  .ssh/authorized_keys

to make sure we haven't added extra keys that you weren't expecting.
~~~

~~~shell
[hadoop@DN02 .ssh]$ ssh-copy-id -i ~/.ssh/id_rsa.pub hadoop@NN01.HadoopVM
The authenticity of host 'nn01.hadoopvm (192.168.71.128)' can't be established.
RSA key fingerprint is 88:22:dc:4b:e4:4b:0b:55:e4:a0:48:03:0d:55:63:7e.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'nn01.hadoopvm,192.168.71.128' (RSA) to the list of known hosts.
hadoop@nn01.hadoopvm's password:
Now try logging into the machine, with "ssh 'hadoop@NN01.HadoopVM'", and check in:

  .ssh/authorized_keys

to make sure we haven't added extra keys that you weren't expecting.
~~~
分发完成后：

~~~shell
[hadoop@NN01 .ssh]$ cat authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAz7rbB/msVLOPHuUEFmowjM2P+Y5wkn0qBm2wiVZRhfW+vaa5HPaR8BTSx+iZQvoDnG55VjFesaQvW3x5rhtV4tQGq9vtSThh7pHifd8EonBAe+Lv+5wqUqmpt/AIb0/94tKhPkEO4LLUePWeCT37omDGtos7lANidtwEyAhpUNtobMTSURnGoFS++ihvaAEwHFXo3UUtkI7+FFJkk/dMhjUMskCyIeKfIoLwOX0FzyrZarOlk/I+P2FH+sl/19nNFvuraJeMtq0KCD/i85bbty2juSRnOOOxr+xjgPNogL6fzU3VC2D1jpRGaqfXDQjXfVLMWv18O61ZKCvVak+GXQ== hadoop@NN01.HadoopVM
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEApxIbV4abM74DR5McJ/Gaa3MuOWGd8VwG3lGSKMF76YEgq3SXKDX6a9x7pfjMCm24vZrj51p1JPQen8ZHPmnKnjj4rhV64NmCHC0RY+/kl0Sr2wUz1t2Xj2MjHZMf0oawOMMMzwcEM/p5x+On1cL7pSwbajfGFJb/+tDKZrsPpBSTWgbwXMTdqZ5A1c2ESj/GhyQSKnhbBoZpSEhPdRTQ/cwuVc9L2d+TtwfuJJnpaIRE+LUox+95PeIsy+fEDVIqOxjBu9vrHSRCPRdQuqt8wD+YykuCFWFj9f89Br0WhRRdfb4PtjbPqP3YXQhTS1JQsxaiFmwNiGub9oBl5ehEgw== hadoop@DN02.HadoopVM
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAua9EJ1g/kjaTERg95goqEQwBUwDu1veN6LD3BE+3yhR4ugkF50OpGxQ7uEQJSWzut09NIOmrirU+NmgYjDrgCDBp4VRtt15G3j4YoTzs+BASyT8y4h39T76fW3xCBBLeBf9Vhrur1lVRdsw5BIQVgznxBYtwvxS/hh2lC9W+fnyOomJd9h2BQ9Gab4V0oX3wkJvbsjut9nN6qeZzLK+SB+rAiM+aGe0z/L2aclLq7sr7LoTsUd/JanN4l/vgrauP0qwAKwBmynz1PfQbaRT2Q44SmQykmVo273DHkch7uqbaVp0mAtW1VFDhPjidIhBW1iSpi3LEdoMp4t01Mc82vQ== hadoop@DN01.HadoopVM
~~~

使用工具将文件copy至DN01和DN02两台机子，如下：

~~~SHELL
[hadoop@NN01 ~]$ scp ./.ssh/authorized_keys  hadoop@DN01.HadoopVM:~/.ssh/authorized_keys
authorized_keys             100% 1203     1.2KB/s   00:00
[hadoop@NN01 ~]$ scp ./.ssh/authorized_keys  hadoop@DN02.HadoopVM:~/.ssh/authorized_keys
authorized_keys             100% 1203     1.2KB/s   00:00
~~~
