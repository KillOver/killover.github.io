---
layout:     post
title:      YARN 工作流程
subtitle:   
date:       2017-11-02
author:     一条小虫
header-img: img/Kafka原理基础/cover.jpg
catalog: true
tags:
    - Kafka
---

# 基本关系

1. 同一topic的数据分散在不同的partition中（每个partition拥有topic的一部分数据，且partion间数据不重复）；
2. 一个group会得到一个topic的**所有**数据。这意味着group会取得每一个partition的数据；
3. group内的consumer之间是负载均衡的关系，各负责一部分，以取得所有partition的数据。一个partition只会被group内的一个consumer消费。
4. 一个partition会同时被不同的group连接，并发地给每个group发数据

# 图解
1. p1只被g2里的一个consumer消费：
	![](/img/Kafka原理基础/relation 1.png)

2. p1、p2、p3一定都会被消费，但是g3里只有2个consumer，所以有一个consumer就需要消费两个partition：
	![](/img/Kafka原理基础/relation 2.png)

3. 每个partition都要被所有group消费到：
	![](/img/Kafka原理基础/relation 3.png)

4. 当partition数量少于某个group内consumer的数量时，这个group内就会有闲置的consumer：
	![](/img/Kafka原理基础/relation 4.png)


