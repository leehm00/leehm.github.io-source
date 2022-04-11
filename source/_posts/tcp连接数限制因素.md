---
title: tcp连接数限制因素
top: false
cover: false
toc: true
mathjax: true
tags:
  - 学习
categories:
  - 实验
typora-root-url: ../
date: 2022-04-01 16:04:51
password:
summary:
---

一般来说一台服务器建立的tcp连接数是有限的，主要是如下原因：

## 打开文件数量方面的系统限制

最高的tcp连接并发数量要受到系统对用户单一进程同时可打开文件数量的限制

系统为每个TCP连接都要创建一个socket句柄，每个socket句柄同时也是一个文件句柄。

```bash
$ unlimit -n
1024
```

unlimit可以查看单个进程打开的文件数限制

linux系统对于用户的打开文件数有软限制和硬限制。

软限制（soft limit）：内核实际执行的限制，任何进程都可以将软限制设置为小于或等于对进程限制的硬限制的值、最大线程数和文件数。可用getrlimit读取，setrlimit设置，参数struct rlimitr.lim_cur. 软限制是限制的当前值，小于等于 硬限制，实际进程可以调用setrlimit增长到硬限制值。也就是说，软限制对进程并不是真正的限制。

硬限制（hard limit）：可以在任何时候任何进程中设置，但硬限制需要由超级用户修改。对进程的资源数的限制的最大值，也可以用getrlimit读取/setrlimit设置，参数struct rlimitr.rlim_max. 硬限制是绝对上限值，进程增长资源数不会超过硬限制。

## 内存资源耗尽

影响一个socket占用内存的参数包括：

rmem_max

wmem_max

tcp_rmem

tcp_wmem

tcp_mem

grep skbuff /proc/slabinfo

每个tcp链接相当于打开了一个文件，文件就是保存在内存中的，当连接数增加时文件数量也会快速增长，虽然文件不大但是蚁多压死象啊。

## 端口限制

一般来说这个不是限制因素



## 查看服务器tcp连接数

```bash
ss  -tan|awk 'NR>1{++S[$1]}END{for (a in S) print a,S[a]}’
```

通过上述命令可以统计出TCP连接中处于各个状态的连接数，在经过Ng代理之后需要真实的连接数需要除以2，因为Client——Nginx——Mqtt其实算是两个TCP连接。

参考：

[软限制和硬限制](https://www.cnblogs.com/fortunely/p/14855912.html)
