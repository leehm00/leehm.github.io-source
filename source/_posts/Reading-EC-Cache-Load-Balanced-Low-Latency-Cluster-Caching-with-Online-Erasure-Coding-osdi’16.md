---
title: >-
  EC-Cache: Load-Balanced, Low-Latency Cluster Caching with Online
  Erasure Coding,osdi’16
top: false
cover: false
toc: true
mathjax: true
tags:
  - 学习
categories:
  - 论文阅读
typora-root-url: ../
date: 2022-04-12 16:58:51
password:
summary:
---

*这其实是一个**读**占大多数的系统*

- 实际上小文件占用磁盘空间不是很大，大文件读的占比更多

> Large Object Reads are Prevalent
>
> only 7% (11%) of the reads are smaller than 1 (10) MB, but their total size in terms of storage usage is miniscule.
>
> Furthermore, 28% of the objects are less than 100 MB in size with less than 5% storage footprint. 

- 小文件

  > a small fraction of the objects are highly popular

网络分布不是很均衡

突发高流量的情况很常见
