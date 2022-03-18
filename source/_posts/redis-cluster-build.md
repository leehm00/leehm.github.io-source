---
title: redis cluster build
top: false
cover: false
toc: true
mathjax: true
tags:
  - redis
categories:
  - 学习
typora-root-url: ../
date: 2022-03-09 17:14:55
password:
summary:
---

# 下载redis6.2.2并安装

```shell
wget http://download.redis.io/releases/redis-6.2.2.tar.gz
tar -zxvf ./redis-6.2.2.tar.gz
mv redis-6.2.2 redis
cd redis
make
```

# 准备配置文件

## 1、新建目录，并拷贝出6个节点的配置文件

```shell
cd  redis-6.2.2
mkdir -p config
mkdir -p log
mkdir -p data
mkdir -p node
cp redis.conf ./config/redis-7000.conf
```

## 2、修改每个节点的配置文件内容

```shell
远程访问需要把bind注释掉
        # bind 127.0.0.1
修改端口号
        port 7000
默认启动时为后台启动，yes为后台启动
        daemonize yes
指定进程信息存储文件
        pidfile /var/run/redis_7000.pid
指定日志文件(改成自己的路径)
        logfile "/home/k8s/homie/redis-6.2.1/log/redis-7000.log" 
指定数据文件路径(改成自己的路径)
        dir "/home/k8s/homie/redis-6.2.1/data"
指定rdb持久化文件名
        dbfilename "dump-7000.rdb"
指定aof持久化文件名（默认情况不会生成，因为默认 appendonly no）  
        appendonly yes
        appendfilename "appendonly-7000.aof"
以集群方式启动
        cluster-enabled yes
集群节点nodes信息配置文件()
        cluster-config-file "/home/k8s/homie/redis-6.2.1/node/nodes-7000.conf"
设置访问密码
        #requirepass redis123
```

## 3、准备其他节点的配置文件

将 redis-7000.conf 文件都复制一份并修改，附sed命令：**sed 's/要被取代的字串/新的字串/g'**

```shell
cd /home/k8s/homie/redis-6.2.1/config
sed "s/7000/7001/g" redis-7000.conf > redis-7001.conf
sed "s/7000/7002/g" redis-7000.conf > redis-7002.conf
sed "s/7000/7003/g" redis-7000.conf > redis-7003.conf
sed "s/7000/7004/g" redis-7000.conf > redis-7004.conf
sed "s/7000/7005/g" redis-7000.conf > redis-7005.conf
```

# 启动6个Redis实列

```shell
cd /home/k8s/homie/redis-6.2.1
./src/redis-server ./config/redis-7000.conf
./src/redis-server ./config/redis-7001.conf
./src/redis-server ./config/redis-7002.conf
./src/redis-server ./config/redis-7003.conf
./src/redis-server ./config/redis-7004.conf
./src/redis-server ./config/redis-7005.conf
```

通过 `ps -ef | grep redis`，查看实例数量，检查是否6个实例已启动。

# 搭建集群

```shell
./src/redis-cli --cluster create 127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 node5:7000 node5:7001 node5:7002 node2:7000 node2:7001 node2:7002 node3:7000 node3:7001 node3:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 node5:7003 node5:7004 node5:7005 node2:7003 node2:7004 node2:7005 node3:7003 node3:7004 node3:7005--cluster-replicas 1
```

​    选项–replicas 1 ： 表示我们希望为集群中的每个主节点创建一个从节点
​    前12个是主节点，后面的是从节点（若节点在不同的机器上，注意主节点的书写位置，要避免主节点在同一台机器上，影响性能）

# 分配槽

一般情况下平均分配好了

```bash
./src/redis-cli -h 127.0.0.1 -p 7000 cluster addslots {0..2730}
./src/redis-cli -h 127.0.0.1 -p 7001 cluster addslots {2731..5461}
./src/redis-cli -h 127.0.0.1 -p 7002 cluster addslots {5462..7211}
./src/redis-cli -h 192.168.1.117 -p 7000 cluster addslots {7212..10922}
./src/redis-cli -h  192.168.1.117 -p 7001 cluster addslots {10923..12331}
./src/redis-cli -h  192.168.1.117 -p 7002 cluster addslots {12332..16383}
```



# 验证集群

## 1、查看集群状态

```bash
./src/redis-cli -h 127.0.0.1 -p 7000  cluster info
```

## 2、查看集群节点信息

```bash
./redis-cli -h 127.0.0.1 -p 7000 cluster nodes
```

可以查看到各节点的信息，槽的分配

# 集群重启

```shell
第一步：先关闭各个Redis节点。

./src/redis-cli -p 7000 shutdown
./src/redis-cli -p 7001 shutdown
./src/redis-cli -p 7002 shutdown
./src/redis-cli -p 7003 shutdown
./src/redis-cli -p 7004 shutdown
./src/redis-cli -p 7005 shutdown


第二步：再启动各个Redis节点即可。
./src/redis-server ./config/redis-7000.conf
./src/redis-server ./config/redis-7001.conf
./src/redis-server ./config/redis-7002.conf
./src/redis-server ./config/redis-7003.conf
./src/redis-server ./config/redis-7004.conf
./src/redis-server ./config/redis-7005.conf
```
