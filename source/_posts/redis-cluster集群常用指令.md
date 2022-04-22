---
title: redis cluster集群常用指令
top: false
cover: false
toc: true
mathjax: true
tags:
  - 学习
categories:
  - redis
typora-root-url: ../
date: 2022-04-22 14:27:37
password:
summary:
---

## 1.redis 常用管理命令

> - https://redis.io/topics/rediscli

### 1.1 读写操作

```
#读写操作$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123 set foo bar$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123 get foo$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123 del foo
```

### 1.2 查看内存等信息

```
#查看内存等信息$redis-cli -h 192.124.64.212 -p 6301 -a pwd123 info$redis-cli -h 192.124.64.212 -p 6301 -a pwd123 info memory |grep human -i$redis-cli -h 192.124.64.212 -p 6301 -a pwd123 dbsize
```

### 1.3 查看key及bigkey

```
#key的情况$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123 dbsize#性能bigkey$redis-cli -c --bigkeys -h 192.124.64.212 -p 6301 -a pwd123#查看key情况$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123  keys '*'  #数据量大时,有风险,该使用scan$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123  scan 0 match '*' count 10001) "0"2) 1) "get"
```

### 1.4 ops及client链接

```
#访问情况$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123 info |grep ops$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123 info clients# Clientsconnected_clients:1$redis-cli -c -h 192.124.64.212 -p 6301 client list -a pwd123 |awk -F'addr=' '{print $2}' | awk '{print $1}'|awk -F':' '{print $1}'|sort |uniq -c|sort -nr
```

### 1.5 统计

```
$redis-cli --stat  -h 192.124.64.212 -p 6301 -a pwd123------- data ------ --------------------- load -------------------- - child -keys       mem      clients blocked requests            connections1          62.49M   1       0       146718 (+0)         60   
```

### 1.6 慢查询

```
#慢查询$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123  SLOWLOG RESET   #清空慢查询#查看$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123  SLOWLOG  GET1) 1) (integer) 4               # 日志的唯一标识符（uid）   2) (integer) 1578413848      # 命令执行时的 UNIX 时间戳   3) (integer) 13              # 命令执行的时长，以微秒计算   4) 1) "SET"                  # 命令以及命令参数      2) "database"      3) "Redis"
```

**时间戳转换:**

- python转换: time.strftime('%Y-%m-%d %H:%M:%S',time.localtime(1578413848))
- MySQL转换: select from_unixtime(1578413848);

### 1.7 配置修改

有时会需要修改配置，如最大内存大小，开aof等。

```
#配置查看/修改$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123 config-admroot get maxmemory$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123 config-admroot set maxmemory 1073741824#aof修改$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123 config-admroot get appendonly1) "appendonly"2) "no"$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123 config-admroot set appendonly yes
#从库只读$redis-cli -h 192.124.64.212 -p 6301  -a pwd123 config get slave-read-only1) "slave-read-only"2) "yes"$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123 config-admroot set slave-read-only yes
```

### 1.8 主从复制

```
#从库同步$redis-cli  -h redis_slave_ip -p redis_slave_port -a xxx  slaveof  redis_master redis_port$redis-cli  -h 192.124.64.212 -p 7301 -a pwd123 slaveof 192.124.64.212 6301#取消同步$redis-cli  -h redis_master -p redis_port slaveof NO ONE# 查看主从同步情况$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123 info Replicationrole:masterconnected_slaves:1slave0:ip=192.124.64.213,port=7301,state=online,offset=114756,lag=0
$redis-cli -c -h 192.124.64.212 -p 7301 -a pwd123 info Replication # Replicationrole:slavemaster_host:192.124.64.214master_port:6301master_link_status:upmaster_last_io_seconds_ago:1
```

## 2. redis cluster常用命令

Redis4.x上面的cluster命令基本上都仍可以在redis5.0.x正常使用。**但是redis5.0.x上redis-trib.rb工具不再可用，redis-trib.rb相关的全部功能，已迁移到redis-cli --cluster命令上了。**

### 2.1 cluster命令语法

```
$redis-cli --cluster SUBCOMMAND [ARGUMENTS] [OPTIONS]
$/usr/local/redis-5.0.6/bin/redis-cli --cluster helpCluster Manager Commands:  create         host1:port1 ... hostN:portN                 --cluster-replicas <arg>  check          host:port                 --cluster-search-multiple-owners  info           host:port  fix            host:port                 --cluster-search-multiple-owners  reshard        host:port                 --cluster-from <arg>                 --cluster-to <arg>                 --cluster-slots <arg>                 --cluster-yes                 --cluster-timeout <arg>                 --cluster-pipeline <arg>                 --cluster-replace  rebalance      host:port                 --cluster-weight <node1=w1...nodeN=wN>                 --cluster-use-empty-masters                 --cluster-timeout <arg>                 --cluster-simulate                 --cluster-pipeline <arg>                 --cluster-threshold <arg>                 --cluster-replace  add-node       new_host:new_port existing_host:existing_port                 --cluster-slave                 --cluster-master-id <arg>  del-node       host:port node_id  call           host:port command arg arg .. arg  set-timeout    host:port milliseconds  import         host:port                 --cluster-from <arg>                 --cluster-copy                 --cluster-replace  help
```

**子命令说明:**

- create：创建集群
- check：检查集群
- info：查看集群信息
- fix：修复集群
- reshard：在线迁移slot
- rebalance：平衡集群节点slot数量
- add-node：将新节点加入集群
- del-node：从集群中删除节点
- set-timeout：设置集群节点间心跳连接的超时时间
- call：在集群全部节点上执行命令
- import：将外部redis数据导入集群

### 2.2 cluster基本信息

基础信息查看, CLUSTER INFO ,CLUSTER NODES等。

```
CLUSTER INFO   # 打印集群的信息CLUSTER NODES   # 列出集群当前已知的所有节点(node),以及这些节点的相关信息。
#示例:$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123 cluster infocluster_state:okcluster_slots_assigned:16384cluster_slots_ok:16384
$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123 cluster nodes |grep master |sort -k 9n5e19efdd5aaed8469fa4900bbda57dd3e88991d6 192.124.64.212:6301@16301 myself,master - 0 15791922727000 1 connected 0-54608021b063dd7c4b6fbcf0d883bf9b9fc460c29eac 192.124.64.213:6301@16301 master - 0 15791922728623 2 connected 5461-1929227f41192121f1365119e5234cd4fe2a89eaf826f30 192.124.64.214:6301@16301 master - 0 15791922728000 3 connected 192923-16383
$redis-cli --cluster info 192.124.64.212:6301 -a pwd123192.124.64.212:6301 (5e19efdd...) -> 1 keys | 5461 slots | 1 slaves.192.124.64.213:6301 (8021b063...) -> 0 keys | 5462 slots | 1 slaves.192.124.64.214:6301 (7f4119212...) -> 0 keys | 5461 slots | 1 slaves.[OK] 1 keys in 3 masters.
$redis-cli --cluster check 192.124.64.212:6301 -a pwd123
```

### 2.3 批量处理

redis-cli **--cluster call 可对集群中全部redis节点批量执行命令,非常好用。**

```
$redis-cli --cluster call   host:port command arg arg .. arg#示例:$redis-cli --cluster info 192.124.64.212:6301 -a pwd123$redis-cli --cluster call 192.124.64.212:6301 info -a pwd123  |grep ops$redis-cli --cluster call 192.124.64.212:6301 info memory -a pwd123 |egrep 'Memory|human'$redis-cli --cluster call 192.124.64.212:6301 info Keyspace -a pwd123$redis-cli --cluster call 192.124.64.212:6301 dbsize -a pwd123$redis-cli --cluster call 192.124.64.212:6301 info clients -a pwd123 | grep Clients -i$redis-cli --cluster call 192.124.64.212:6301 client list -a pwd123
```

### 2.4 节点管理

```
CLUSTER MEET <ip> <port>   # 将 ip 和 port 所指定的节点添加到集群当中，让它成为集群的一份子。CLUSTER FORGET <node_id>   # 从集群中移除 node_id 指定的节点。CLUSTER REPLICATE <node_id>   # 将当前节点设置为 node_id 指定的节点的从节点。CLUSTER SAVECONFIG   # 将节点的配置文件保存到硬盘里面。CLUSTER NODES   # 列出集群当前已知的所有节点(node),以及这些节点的相关信息。CLUSTER SLAVES node-id   # 返回一个master节点的slaves 列表
#cluster forget 命令需要在集群内所有节点执行$redis-cli --cluster call  192.124.64.212:6301 cluster FORGET e4adb3835xx -a pwd123
```

**集群扩容redis节点:**

- 扩容master: 把新节点加入集群，reshard 迁移slot到新节点，reblance集群。
- 扩容slave: 需要指定--cluster-master-id ，省去了迁移slot的步骤。

```
add-node     new_host:new_port existing_host:existing_port                 --cluster-slave                 --cluster-master-id <arg>del-node       host:port node_id#节点查看$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123  cluster nodes
#添加master节点$redis-cli --cluster add-node   new_host:new_port existing_host:existing_port$redis-cli --cluster add-node 192.124.64.212:6543 192.124.64.212:6301 -a pwd123
#分配slot$redis-cli --cluster info  192.124.64.212:6301 -a pwd123$redis-cli --cluster check  192.124.64.212:6301 -a pwd123#reshard ,迁移slotredis-cli --cluster reshard  192.124.64.212:7543 --cluster-from 7f4110121f1365119e5234cd4fe2a89eaf826f30 --cluster-to 5c524853ad5995cd30cad27adce042c9d1ad55ce --cluster-slots 4 -a pwd123
#rebalance$redis-cli --cluster rebalance 192.124.64.212:6301 -a pwd123
#添加slave节点,--slave 表示是添加从库$redis-cli --cluster  add-node --cluster-slave --cluster-master-id xxx new_host:new_port existing_host:existing_port$redis-cli --cluster  add-node   192.124.64.212:6543 192.124.64.212:6301 --cluster-slave --cluster-master-id 5e19efdd5aaed8xx -a pwd123>>> Send CLUSTER MEET to node 192.124.64.212:6543 to make it join the cluster.Waiting for the cluster to join>>> Configure node as replica of 192.124.64.212:6301.[OK] New node added correctly.
#检查$redis-cli --cluster info 192.124.64.212:6301 -a pwd123$redis-cli --cluster check 192.124.64.212:6301 -a pwd123
```

**删除节点:**

- 删除主库节点: 只能删除没有分配slot的节点,故先移除删除节点的slot，然后del-node。
- 删除从库节点: 直接用--cluster del-node 命令删除即可。

```
#删除节点#redis-cli --cluster del-node host:port node_id$redis-cli --cluster  del-node host:port node_id$redis-cli -a pwd123 --cluster  del-node  192.124.64.212:7543  5c524853ad5995cd30cad27adce042c9d1ad55ce>>> Removing node 5c524853ad5995cd30cad27adce042c9d1ad55ce from cluster 192.124.64.212:7543>>> Sending CLUSTER FORGET messages to the cluster...>>> SHUTDOWN the node.
#若是删除失败可能要用到forget命令$redis-cli --cluster call  192.124.64.212:6301 cluster FORGET 5c524853ad59xx -a pwd123
```

### 2.5 SLOT处理

```
CLUSTER KEYSLOT <key>   # 计算键 key 应该被放置在哪个槽上。CLUSTER COUNTKEYSINSLOT <slot>   # 返回槽 slot 目前包含的键值对数量。CLUSTER GETKEYSINSLOT <slot> <count>   # 返回 count 个 slot 槽中的键。#CLUSTER ADDSLOTS <slot> [slot ...]   # 将一个或多个槽（slot）指派（assign）给当前节点。CLUSTER DELSLOTS <slot> [slot ...]   # 移除一个或多个槽对当前节点的指派。CLUSTER FLUSHSLOTS   # 移除指派给当前节点的所有槽，让当前节点变成一个没有指派任何槽的节点。CLUSTER SETSLOT <slot> NODE <node_id>   # 将槽 slot 指派给 node_id 指定的节点。CLUSTER SETSLOT <slot> MIGRATING <node_id>   # 将本节点的槽 slot 迁移到 node_id 指定的节点中。CLUSTER SETSLOT <slot> IMPORTING <node_id>   # 从 node_id 指定的节点中导入槽 slot 到本节点。CLUSTER SETSLOT <slot> STABLE   # 取消对槽 slot 的导入（import）或者迁移（migrate）。
```

**slot操作示例:**

```
#计算$redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123 CLUSTER KEYSLOT foo(integer) 12182
#检查slot$redis-cli --cluster info  192.124.64.212:6301 -a pwd123$redis-cli --cluster check  192.124.64.212:6301 -a pwd123
#迁移slot$redis-cli --cluster reshard  192.124.64.212:7543 --cluster-from 5c524853ad5995cd30cad27adce042c9d1ad55ce --cluster-to 7f4110121f1365119e5234cd4fe2a89eaf826f30  --cluster-slots 4 -a pwd123
#增加/删除槽redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123  CLUSTER ADDSLOTS 16383redis-cli -c -h 192.124.64.212 -p 6301 -a pwd123  CLUSTER DELSLOTS 16383
```

### 2.7 Reshard及Rebalance

虽然reshard和rebalance也是slot操作相关,但因为工作常会使用并且命令参数较多，故单独列出介绍。

**Reshard :**

手动迁移slot很烦琐，容易出错，所以redis-cli提供了reshard 子命令来支持在线迁移slot。

```
 reshard      host:port                 --cluster-from <arg>                 --cluster-to <arg>                 --cluster-slots <arg>                 --cluster-yes                 --cluster-timeout <arg>                 --cluster-pipeline <arg>                 --cluster-replace
参数说明：host：port：必传参数，集群内任意节点地址，用来获取整个集群信息。--cluster-from：制定源节点的id，如果有多个源节点，使用逗号分隔，如果是all源节点变为集群内所有主节点，在迁移过程中提示用户输入。--cluster-to：需要迁移的目标节点的id，目标节点只能填写一个，在迁移过程中提示用户输入。--cluster-slots：需要迁移槽的总数量，在迁移过程中提示用户输入。--cluster-yes：当打印出reshard执行计划时，是否需要用户输入yes确认后再执行reshard。--cluster-timeout：控制每次migrate操作的超时时间，默认为60000毫秒。--cluster-pipeline：控制每次批量迁移键的数量，默认为10。

#示例$redis-cli  --cluster reshard  host:port$redis-cli --cluster reshard  192.124.64.212:6301 --cluster-from 5c524853ad5995cd30cad27adce042c9d1ad55ce --cluster-to 7f4110121f1365119e5234cd4fe2a89eaf826f30  --cluster-slots 4 -a pwd123
```

**Rebalance :**

若各节点slot数量不均衡时，可以用rebalance命令来平衡各集群节点slot数量。

```
rebalance       host:port                 --cluster-weight <node1=w1...nodeN=wN>                 --cluster-use-empty-masters                 --cluster-timeout <arg>                 --cluster-simulate                 --cluster-pipeline <arg>                 --cluster-threshold <arg>                 --cluster-replace
host:port：这个是必传参数，用来从一个节点获取整个集群信息，相当于获取集群信息的入口。--cluster-weight <arg>：节点的权重，格式为node_id=weight,例如--weight b31e3a2e=5 node_id可为节点名称的前缀。没有传递–weight的节点的权重默认为1。--cluster-threshold <arg>：只有节点需要迁移的slot阈值超过threshold，才会执行rebalance操作。--cluster-use-empty-masters：rebalance是否考虑没有节点的master，默认没有分配slot节点的master是不参与rebalance的，设置--use-empty-masters可以让没有分配slot的节点参与rebalance。--cluster-timeout <arg>：设置migrate命令的超时时间。--cluster-simulate：设置该参数，可以模拟rebalance操作，提示用户会迁移哪些slots，而不会真正执行迁移操作。--cluster-pipeline <arg>：与reshar的pipeline参数一样，定义cluster getkeysinslot命令一次取出的key数量，不传的话使用默认值为10。
#示例#rebalanceredis-cli --cluster rebalance host:port$redis-cli --cluster rebalance 192.124.64.212:6301 -a pwd123$redis-cli --cluster rebalance 192.124.64.212:6301 -a pwd123 --cluster-threshold 1 --cluster-use-empty-masters --pipeline 10
```

## 参考:

- https://redis.io/documentation 
- https://redis.io/topics/cluster-tutorial
- https://redis.io/topics/rediscli
