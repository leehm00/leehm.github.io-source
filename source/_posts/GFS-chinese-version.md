---
title: GFS chinese version
top: false
cover: false
toc: true
mathjax: true
tags:
  - 论文
categories:
  - 学习
typora-root-url: ../
date: 2022-02-15 19:17:00
password:
summary:
---

# The Google File System中文版

2020-04-18 | [论文](https://chunlife.top/categories/论文/)

The Google File System

> ## GFS MapReduce BigTable关系
>
> GFS（2003年发表）使用商用硬件集群存储海量数据。文件系统将数据在节点之间冗余复制。MapReduce（2004）是GFS架构的一个补充，因为它能够充分利用GFS集群中所有低价服务器提供的大量CPU。它与GFS一道形成了处理海量数据的核心力量，包括构建Google的搜索索引。不过这两个系统都缺乏实时随机存取数据的能力，意味着尚不足以处理Web服务。
>
> GFS的另一个缺陷就是，它适合存储少许非常非常大的文件，而不适合存储成千数万的小文件，例如社交平台上的图片，因为文件的无数据信息最终要存储在主节点的内存中，文件越多master的压力越大。
>
> 这时候需要一个能够驱动交互应用的解决方案，且能够同时利用以上两种基础架构和依靠GFS 存储的数据冗余和数据可用性较强的特点。存储的数据应该拆分成特别小的条目，然后由系统将这些小记录聚合到非常大的文件中，并提供一些索引排序，让用户可以查找最少的磁盘就能够获取到数据。最终，它要能够及时存储爬虫的结果，并跟MapReduce协作生成搜索索引。于是考虑放弃关系型的特点，采用简单的API来进行增删改查操作，另加一个扫描函数，以在较大的键范围或全表上迭代扫描，最终形成一个管理结构化数据的分布式存储系统BigTable（2006）。
>
> 值得一提的是CAP定理，当中指出，一个分布式系统只能同时实现一致性、可用性和分区容忍性（独立性）中的两个，不可能三者兼顾。放宽一致性的要求会提升系统的可用性。

[GFS中文版](GFS-chinese-version/The Google File System中文版.pdf)
