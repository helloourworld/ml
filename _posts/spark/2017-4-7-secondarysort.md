---
layout: post
title: Secondary Sort
category: Spark
catalog: yes
description: Secondary Sort 实际上就是一种对Value进行二次排序
tags:
    - Spark
    - Python
---
## secondarysort

data:

~~~
20 21
50 51
50 52
50 53
50 54
60 51
60 53
60 52
60 56
60 57
70 58
60 61
70 54
70 55
70 56
70 57
70 58
10 55
80 67
90 43
30 44
50 67
50 87
40 77
20 11
10 55
20 84
70 45
90 55
91 44
78 44
76 32
88 23
91 34
56 11
33 23
24 11
~~~

<script src="https://gist.github.com/helloourworld/2807f6f5487b78e3d757a6f866d48421.js"></script>


<script src="https://gist.github.com/helloourworld/a1f69e58cd431e055d0eed5d230c04bd.js"></script>

<script src="https://gist.github.com/helloourworld/d44ddc077fc707a2eef4d239f42ac78f.js"></script>

参考： http://spark.apache.org/docs/2.0.1/api/python/pyspark.html#module-pyspark
