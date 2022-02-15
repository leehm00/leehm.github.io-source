---
title: MIT 6.824 2022 notes 1 chinese version
top: false
cover: false
toc: true
mathjax: true
tags:
  - course
categories:
  - 学习
typora-root-url: ../
date: 2022-02-16 00:06:31
password:
summary:
---

# chinese version

## 课程背景

构建分布式系统的原因：

1. Parallelism，资源并行（提高效率）。
2. Fault tolerance，容错。
3. Physical，系统内在的物理分散。
4. Security，不可信对端（区块链）。

分布式系统面临的挑战：

1. Concurrency，系统构件很多，并行繁杂，交互复杂。
2. Partial failure，存在部分失败，而不是像单机一样要么正常运行，要么完全宕机。
3. Performance，精巧设计才能获取与机器数量线性相关的性能。



## 课程组成

1. Lectures，授课，一些案例学习。
2. Papers，论文。
   - 包括一些经典的和前沿的、学术的和工业界的。
   - 看其观点，学其实现，断其性能。
   - 抓重要部分，略次要部分。
   - 课程主页有所有论文链接。
3. Exams，期中期末两次考试。
4. Labs：四个实验
   - lab1： MapReduce
   - lab2： Raft 容错
   - lab3： K/V server use Raft
   - lab4： Shared K/V based on lab3
     分布式系统巨难调试，做好心理准备，早点开做。
5. Project，可以自选相关题目，组队完成，用来替代 lab4。

## 课程内容

本课程旨在学习支撑应用的基础设施**抽象**（abstraction），包括

1. Storage，存储，一个很直接并常用的抽象；如何构建多副本、容错、高性能分布式存储系统。
2. Communication，通信，如何可靠的通信。
3. Computation，现代的大规模计算，如 MapReduce

最终理想是提供能够屏蔽分布式细节的、类似于单机的通用接口，同时能兼具**容错**和**性能**。

对于上述抽象，我们有哪些实现呢？

1. RPC：像在本机调用一样跨节点通信
2. Concurrency，Threads：并发载体
3. Concurrency，Lock：并发控制。

### Performance 性能

scalability，可扩展性

- 可以线性的集结计算机资源：使用两倍的机器获取两倍的吞吐。
- 意味着遇到瓶颈你只需要花少量的钱买机器，而不用付很多的工资找程序员重构。
- 但这个特点很难实现。通常你将一个组件扩展后，瓶颈就转移到了另一个组件，全组件的无限扩展很难。

### Fault Tolerance 容错

单机虽好，作为上千台机器组成的集群来说，故障却是常态。比如说：

- 主机宕机
- 网络抖动
- 交换机故障

Availability 可用性
Recoverbility 可恢复性，无干预 、不影响正确性的可恢复

手段：
NV storage：持久化
Replication：多副本

### Consistency 一致性

分布式系统产生不一致的因素：

1. 缓存
2. 多副本

不同程度的一致性：

1. 强一致性：每个客户端每次都能读到（自己 or 他人）之前所写数据。在多副本系统实现强一致性代价十分高昂，需要进行大量的通信。简单说两种方法：
   - 每次更改同时写到所有副本
   - 每次读取都去读所有副本，使用具有最新时间戳的数据。
2. 弱一致性，为了性能，工业级系统通常选择弱一致性。

## MapReduce

### 背景

Google （2003 年左右）面对巨量（数十 T）的索引数据和全网结构的数据，需要找到最重要的网页。可以简化为一个排序问题，但如此数量级的排序，单机不是一个可选项。而又不是所有工程师都有手撸分布式系统的能力，因此产生了做一个分布式框架的需求，以对应用程序员屏蔽分布式环境细节：

1. 如何将工作高效分配到上千台机器上。
2. 如何控制数据流动。
3. 如何进行容错。

等等。

### 工作原理

以 WordCount 为例：

**Map**: document -> (word, 1)

**Shuffle**：group by word in Map machine，send each key Range to the corresponding Reduce Machine。

**Reduce**: List(word, 1) -> (word, count)

### 术语体系

**任务**：Job

**工作**：Task，分为 Map Task 和 Reduce Task。

**工作节点**：worker server

**工作进程**：worker process

**主节点**：master server

### 存储配合

为了更好的并行读写，需要一个网络文件系统来配合输入和输出，这就是 GFS（谷歌文件系统）。

GFS 可以简单理解为，一个将大文件拆为一个个小的 64M 的块分散到不同机器上网络文件系统。

### 网络开销

为了尽量绕开当时的主要瓶颈（网络传输），Google 做了一系列优化，包括 GFS 和 MR 跑在一个集群上，以减少读取和写入数据的网络传输。具体做法是让 Map 任务（Map Task）去找数据（Block）—— 将 Task 调度到其输入所在的机器上。但对于 Reduce 任务，无论如何都会存在大量网络开销：GFS 对数据都进行了冗余备份，意味着每个结果都要写多次。

不过，时下的数据中心可以通过很多手段使得网络传输的速度大大提高，比如使用多个根路由器进行分摊流量，意味着在设计时可以有更多灵活性，不用太为网络传输而优化。

*中文版作者：木鸟杂记 https://www.qtmuniao.com/2020/02/29/6-824-video-notes-1/, 转载请注明出处*

**群号为：813098424 入群条件为独立完成第一个lab Map&Reduce** 实际上，第一个lab的难度并不大，可以说是热身性质的lab。入群请给出完成该lab的对应的repo的网址，管理员会审核是否满足条件。**入群之后，请立刻将repo设置为private，避免影响其他学习这门课程的同学。**

