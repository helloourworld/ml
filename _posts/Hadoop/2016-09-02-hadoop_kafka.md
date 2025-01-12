﻿---
layout: post
title: kafka组件深度解析
category: Hadoop
catalog: yes
description:  kafka组件深度解析
tags:
    - Big Data
    - 大数据
---

<body class="theme theme-white">
<div id="wmd-preview" class="wmd-preview wmd-preview-full-reader"><div class="md-section-divider"></div><div class="md-section-divider"></div><h1 data-anchor-id="sm5m" id="kafka组件深度解析">kafka组件深度解析</h1><p data-anchor-id="i7gx"><code>kafka</code></p><hr><div class="md-section-divider"></div><h2 data-anchor-id="9ot1" id="1kafka介绍">1,kafka介绍</h2><p data-anchor-id="rxtu">关于kafka入门的文章最好的就莫过于kafka的官方文档了，这上面对kafka的定义是：</p><div class="md-section-divider"></div><pre class="prettyprint linenums prettyprinted" data-anchor-id="9uc9"><ol class="linenums"><li class="L0"><code><span class="typ">Kafka</span><span class="pln"> </span><span class="kwd">is</span><span class="pln"> a distributed</span><span class="pun">,</span><span class="pln"> partitioned</span><span class="pun">,</span><span class="pln"> replicated commit log service</span><span class="pun">.</span><span class="pln"> </span><span class="typ">It</span><span class="pln"> provides the functionality of a messaging system</span><span class="pun">,</span><span class="pln"> but </span><span class="kwd">with</span><span class="pln"> a unique design</span><span class="pun">.</span></code></li></ol></pre><p data-anchor-id="n4w5">kafka是一个分布式的，可分区的，可备份的日志提交服务，它使用独特的设计实现了一个消息系统的功能。 <br>
本文主要介绍一下kafka集群的基本结构和kafka的一些专业术语。</p><div class="md-section-divider"></div><h2 data-anchor-id="cugo" id="2kafka集群的基本架构">2,kafka集群的基本架构</h2><p data-anchor-id="p523">一个典型的kafka集群架构如下图所示： <br>
<img src="http://static.zybuluo.com/hadoopMan/1p7hrd1dghk77nls3kq0a5z9/image_1ark929smq0hs1v14di1auauqe9.png" alt="image_1ark929smq0hs1v14di1auauqe9.png-74.2kB"> <br>
kafka集群常用的场景就是，producer把日志信息推送（push）到broker节点上，然后consumer（可以是写入到hdfs或者其他的一些应用）再从broker拉取（pull）信息。kafka的push&amp;pull机制如下图所示。 <br>
<img src="http://static.zybuluo.com/hadoopMan/n8qrw5kjbzz54jlyuw5cpcyv/image_1ark934lind8121k7k965eodm.png" alt="image_1ark934lind8121k7k965eodm.png-11.7kB" title=""> <br>
作为一个message system，kafka遵循了传统的方式，选择由kafka的producer向broker push信息，而consumer从broker pull信息。kafka的consumer之所以没有采用push模式，主要是因为push模式很难适应速率不同的consumer，因为消息发送速率是由broker决定的。push模式的目标就是尽可能以最快速度传递消息，但是这样很容易造成consumer来不及处理消息，典型的表现就是拒绝服务以及网络拥塞，而pull模式则可以根据consumer的消费能力以适当的速率消费message。</p><div class="md-section-divider"></div><h2 data-anchor-id="cs4s" id="3kafka相关的专业术语">3,kafka相关的专业术语</h2><p data-anchor-id="pxzn">kafka使用的一些主要的专业术语： <br>
Topic：特指Kafka处理的消息源的不同分类，其实也可以理解为对不同消息源的区分的一个标识； <br>
Partition：Topic物理上的分组，一个topic可以设置为多个partition，每个partition都是一个有序的队列，partition中的每条消息都会被分配一个有序的id（offset）； <br>
Message：消息，是通信的基本单位，每个producer可以向一个topic（主题）发送一些消息； <br>
Producers：消息和数据生产者，向Kafka的一个topic发送消息的过程叫做producers（producer可以选择向topic哪一个partition发送数据）。 <br>
Consumers：消息和数据消费者，接收topics并处理其发布的消息的过程叫做consumer，同一个topic的数据可以被多个consumer接收； <br>
Broker：缓存代理，Kafka集群中的一台或多台服务器统称为broker。 <br>
理解上述概念之后，再来看kafka就容易了。</p><div class="md-section-divider"></div><h2 data-anchor-id="vmn6" id="4kafka的应用场景">4,kafka的应用场景</h2><p data-anchor-id="to3q">Kafka主要用于处理流式数据。流式数据在web网站应用中非常常见，这些数据包括网站的pv、用户访问了什么内容，搜索了什么内容等。这些数据通常以日志的形式记录下来，然后每隔一段时间进行一次统计处理。Kafka的作用类似于缓存，能够很好地处理实时和离线应用。</p><div class="md-section-divider"></div><h2 data-anchor-id="d7e2" id="5kafka组件详解">5,kafka组件详解</h2><div class="md-section-divider"></div><h3 data-anchor-id="cw7l" id="组件一topic">组件一：topic</h3><p data-anchor-id="7rz8">正如前面介绍的，topic是一个kafka发送消息的目录，比如，对于一个有三个partition的topic而言，它日志信息结构大概如下图所示： <br>
<img src="http://static.zybuluo.com/hadoopMan/89auzb14fsdju5ruplm9ieb1/image_1ark96cth19t31hsbd261k7pded13.png" alt="image_1ark96cth19t31hsbd261k7pded13.png-34.8kB" title=""> <br>
每一个partition实际上都是一个有序的，不可变的消息序列，producer发送到broker的消息会写入到相应的partition目录下，每个partition都会有一个有序的id（offset），这个offset确定这个消息在partition中的具体位置。</p><p data-anchor-id="45rw">举一个例子，我们在kafka集群中建立的一个名为wangzzu、partition数为3的topic，kafka就会在broker的/tmp/kafka-logs新建三个目录，这里我们直接指定将三个partition建立在同一个broker上，如下图所示： <br>
<img src="http://static.zybuluo.com/hadoopMan/h637rbat273zpq91kdv8kj8o/image_1ark96shjtnd1sbv1pdm8i187f1g.png" alt="image_1ark96shjtnd1sbv1pdm8i187f1g.png-87.1kB" title=""> <br>
当启动producer程序时，就会向kafka集群发送信息，而kafka就会把中间信息存储在这三个目录下。</p><div class="md-section-divider"></div><h3 data-anchor-id="fw2s" id="组件二producer">组件二：producer</h3><p data-anchor-id="5ar0">producer这部分相比较而言，是比较简单的，就是把消息发送给它所选择的topic，也可以具体指定发给这个topic的哪个一个partition，否则producer就会使用hashing-based partitioner来决定发送到哪个partition，这个问题还是需要多说一些，之前我在测试kafka速度的时候就遇到了这个问题，当我们增加broker的数量时，kafka的发送速度并没有线性增加，最后发现就是因为这个原因，没有指明发送数据到哪个partition，具体的解释我就引用官网WIKI中给出回答：</p><p data-anchor-id="lw8n">In Kafka producer, a partition key can be specified to indicate the destination partition of the message. By default, a hashing-based partitioner is used to determine the partition id given the key, and people can use customized partitioners also. <br>
To reduce # of open sockets, in 0.8.0(High number of open file handles in 0.8 producer), when the partitioning key is not specified or null, a producer will pick a random partition and stick to it for some time (default is 10 mins) before switching to another one. So, if there are fewer producers than partitions, at a given point of time, some partitions may not receive any data. To alleviate this problem, one can either reduce the metadata refresh interval or specify a message key and a customized random partitioner.</p><div class="md-section-divider"></div><h3 data-anchor-id="qbon" id="组件三consumer">组件三：consumer</h3><p data-anchor-id="rk9i">这里的consumer部分，主要是以High Level Consumer API为例。</p><p data-anchor-id="1794">consumer是一个抽象的概念，调用Consumer API的程序都可以称作为一个consumer，它从broker端订阅某个topic的消息。如果只有一个consumer的话，该topic（可能含有多个partition）下所有消息都会被这个consumer接收。但是在分布式的环境中，我们可能会遇到这样一种情景，对于一个有多个partition的topic，我们希望启动多个consumer去消费这些partition（如果发送速度较快，一个consumer是无法消费完的），并且要求topic的一条消息只能发给其中一个consumer，不希望这些conusmer出现重复接收一条消息的情况。对于这种情况，我们应该怎么办呢？kafka给我们提供了一种机制，可以很好来适应这种情况，那就是consumer group（当然也可以应用在第一种情况，实际上，如果只有一个consumer时，是不需要指定consumer group，这时kafka会自动给这个consumer生成一个group名）。</p><p data-anchor-id="uwxk">在调用conusmer API时，一般都会指定一个consumer group，该group订阅的topic的每一条消息都发送到这个group的某一台机器上。借用官网一张图来详细介绍一下这种情况，假如kafka集群有两台broker，集群上有一个topic，它有4个partition，partition 0和1在broker1上，partition 2和3在broker2上，这时有两个consumer group同时订阅这个topic，其中一个group有2个consumer，另一个consumer有4个consumer，则它们的订阅消息情况如下图所示： <br>
<img src="http://static.zybuluo.com/hadoopMan/ipd707bsbhodj41wt5w68wbj/image_1ark98tannofe4c1bie13rv1m7j1t.png" alt="image_1ark98tannofe4c1bie13rv1m7j1t.png-46.3kB"> <br>
因为group A只有两个consumer，所以一个consumer会消费两个partition；而group B有4个consumer，一个consumer会去消费一个partition。这里要注意的是，kafka可以保证一个partition内的数据是有序的，所以group B中的consumer收到的数据是可以保证有序的，但是Group A中的consumer就无法保证了。</p><p data-anchor-id="cfqj">group读取topic，partition分配机制是： <br>
如果group中的consumer数小于topic中的partition数，那么group中的consumer就会消费多个partition； <br>
如果group中的consumer数等于topic中的partition数，那么group中的一个consumer就会消费topic中的一个partition； <br>
如果group中的consumer数大于topic中的partition数，那么group中就会有一部分的consumer处于空闲状态。</p><div class="md-section-divider"></div><h2 data-anchor-id="3bb0" id="6kafka的简单使用">6,kafka的简单使用</h2><p data-anchor-id="hmpn">这部分是利用kafka自带的kafka-console-producer.sh和kafka-console-consumer.sh来发送和接收消息，而具体如何调用 kafka API使用kafka会在后续的文章中介绍。</p><p data-anchor-id="5we8">首先要启动kafka和建立topic： <br>
nohup bin/kafka-server-start.sh config/server.properties &amp; #启动kafka，并且使用nohup将日志输出到当前目录的nohup.out中，使用&amp;后台运行</p><p data-anchor-id="j0vr">topic还接着使用wangzzu（建立topic命令参考2.1中的图片），下面开启kafka-console-producer.sh并发送几条消息： <br>
<img src="http://static.zybuluo.com/hadoopMan/caf8ml026ckq84e108ofjpy4/image_1ark9a3341enq3271ovipua4s22a.png" alt="image_1ark9a3341enq3271ovipua4s22a.png-45.4kB" title=""> <br>
然后，启动kafka-console-consumer.sh就可以收到我们发送的这几条消息： <br>
<img src="http://static.zybuluo.com/hadoopMan/21mfgg90p68caz9fgezbj382/image_1ark9a8du9qc1m2g8sttnpfma2n.png" alt="image_1ark9a8du9qc1m2g8sttnpfma2n.png-27.9kB" title=""> <br>
这个就是kafka的最简单的使用情况了。 <br>
希望这篇文章对初学者能有所帮助。</p></div>
</body>
</html>
