---
layout: post
title: 远程访问jupyter notebook
category: MLAdvance
catalog: yes
description: 默认只能在本地访问，如果想把它安装在服务器上，然后在本地远程访问，则需要进行如下配置
tags:
    - Python
    - IT
---

1. 登陆远程服务器
2. 生成配置文件

~~~
jupyter notebook --generate-config
~~~

3. 生成密码

打开ipython，创建一个密文的密码：

~~~
In [1]: from notebook.auth import passwd
In [2]: passwd()
Enter password:
Verify password:
Out[2]: 'sha1:ce23d945972f:34769685a7ccd3d08c84a18c63968a41f1140274'
~~~

4. 修改默认配置文件

$vim ~/.jupyter/jupyter_notebook_config.py
进行如下修改：

~~~
c.NotebookApp.ip='*'
c.NotebookApp.password = u'sha:ce...刚才复制的那个密文'
c.NotebookApp.open_browser = False
c.NotebookApp.port =8888 #随便指定一个端口
~~~
