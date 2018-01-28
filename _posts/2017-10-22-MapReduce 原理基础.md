---
layout:     post
title:      MapReduce 原理基础
subtitle:   
date:       2017-10-22
author:     一条小虫
header-img: img/MapReduce基础/MapReduce 2.png
catalog: true
tags:
    - MapReduce
---

> MapReduce的思想：**分治**（分解 - 求解 - 合并）。

# MapReduce工作流程

如下两张图表示了MapReduce的工作流程：

![](/img/MapReduce基础/MapReduce.png)

![](/img/MapReduce基础/MapReduce 2.png)

- map的结果进buffer，是为了排序；buffer满了就flush然后生成一个spill，会得到很多spill；
- 每个spill是按照{partition，key，value}排序的，其中partition是key所对应的hash值（partition有多少个，就有多少个reducer）；
- 所有spill按照{partition，key，value}继续进行归并排序，于是产生了一个大的最终的spill，其内部也是根据{partition，key，value}划分的；
- 所有map产生的大spill中，相同partition的会被汇总到一个地方，然后进行合并merge（归并排序），直到生成一个大的、拥有同一个partition值的、结果按key排序的大spill，作为reducer的输入（一个reduce task中，所有spill的key对应的hash是相同的，也就是partition值）；
- reduce方法实体对经过汇总的spill进行处理。

总的来说，Map的作用是生成 {key, value}，然后Hadoop集群会把同一个key的集合到一起，得到一个{key, [value1, value2, ...]}作为reduce的输入，reduce处理完就得到结果了。

>  所以说key的数量的控制、每个key所对应的计算量的均衡（不要某一个key计算了10分钟，其它key计算了1秒钟），是MR编程的一个人为掌控的地方，也是功夫所在。

#  Map和Reduce的运行与存储

MapReduce分为Map任务和Reduce任务。一个任务失败，它将在另一个不同的节点上自动运行。

map的输出是整个计算任务的中间结果，一般会存储在本地磁盘上。因为如果这个中间结果也在HDFS中自动备份的话小题大做了。

reduce的输出结果存在HDFS上可靠存储，且不具备本地化优势，它的数据来自各个节点上map输出。


# Map分片大小

Map的输入数据叫“输入分片”（简称“分片”）。分片会在map数据从处理、按key分组。

分片越小，则总耗时越少（共同处理的机器多了），同时负载均衡也更好（快的机器和慢的机器总的时间差更小）；

分片过小，管理分片的总时间和构建map任务的总时间就会占据更大的比重。

合理的分片大小是一个HDFS块的大小，默认是128M。
> 因为如果分片包括2个HDFS数据块，任意一个HDFS节点都几乎不可能同时存储这2个数据块，就需要网络传输了。


