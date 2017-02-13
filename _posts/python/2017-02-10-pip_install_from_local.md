---
layout: post
title: 断网环境下利用pip安装Python离线安装包
category: MLAdvance
catalog: yes
description: 离线断网环境下安装Python包，配置环境
tags:
    - Python
    - IT
---

* 1.在可以联网的开发机器上安装好需要的包
* 例如：

~~~
pip install numpy
pip install pandas
~~~

* 2.打包已安装的包
* 在python目录下新建packages文件夹用来存储下载下来的所需安装包。

~~~
pip list #查看安装的包
pip freeze >requirements.txt
pip install --download packages -r requirements.txt
~~~

* 3.离线情况安装打包好的包
* 将packages文件夹和requirement.txt拷贝至离线机器上目录下，packages文件夹放在Python目录下，requirement.txt放在Scripts目录下。

~~~
pip install --no-index --find-index=d:\python27\packages -r requirements.txt
~~~

补充

1.下载指定的包到指定文件夹

~~~
pip install --download d:\python27\packs pandas（-r requirements.txt）
~~~

2.安装指定的离线包

~~~
pip install --no-index --find-links=d:\python27\packs\ pandas （-r requirements.txt）
~~~

3. [stackoverflow-about-python](https://taizilongxu.gitbooks.io/stackoverflow-about-python/content/8/README.html)

找不到vcvarsall.bat
