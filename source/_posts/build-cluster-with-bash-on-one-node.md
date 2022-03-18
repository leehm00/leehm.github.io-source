---
title: build cluster with bash on one node
top: false
cover: false
toc: true
mathjax: true
tags:
  - redis
categories:
  - 学习
typora-root-url: ../
date: 2022-03-16 20:50:28
password:
summary:
---

在我自己的机器上进行实验，sh文件实现一条命令部署单个节点集群

下面是解析步骤

## 常量定义

定义在另一个文件中

```bash
#!/bin/bash

# Redis_Cluster

# REDIS_SERVER_BIN="/home/k8s/homie/redis/src/redis-server"
BASE_DIR=/home/k8s/homie
REDIS_SERVER_BIN=$BASE_DIR/redis/src/redis-server
REDIS_CLI_BIN=$BASE_DIR/redis/src/redis-cli
REDIS_CLUSTER_BASE=$BASE_DIR/redis_cluster
NODELIST="node2 node3 node4 node5"
PORTS=`seq 7000 7005`
BASEPORT=${ENTRY_PORT}
HG_BASEPORT=${HG_PORT}
```

根据自己需要进行修改

## 安装redis

```bash
# Check redis command
if [ ! -f $REDIS_SERVER_BIN ]; then
  echo "Redis not ready, please install redis firstly!"
  echo ""
  echo "===== Install redis as follows ====="
  cd $BASE_DIR
  wget http://download.redis.io/releases/redis-6.2.2.tar.gz
  tar -zxvf ./redis-6.2.2.tar.gz
  mv redis-6.2.2 redis
  cd redis
  make
  cd $BASE_DIR
  echo ""
  echo "Redis Server Installation Finished!"
fi
```

没有安装redis的话就安装，这里使用的是6.2.2版本。

## 删除节点配置函数

```bash
function remove_cluster() {
  # kill redis servers
  ps -ef | grep redis-server | grep cluster | awk '{print $2}' | xargs kill -9 
  # disable systemd
  # systemctl disable redis-cluster.service
  # rm cluster data
  if [ -d "$REDIS_CLUSTER_BASE" ]; then
    rm -rf $REDIS_CLUSTER_BASE
  fi
}

if [ "$1" = "--remove" ]; then
  remove_cluster  
  exit 0
fi
```

因为我没有配置随系统开启，所以直接找出进程kill就行

## 建立工作文件夹

```bash
# enter work directory
mkdir -p $REDIS_CLUSTER_BASE
cd $REDIS_CLUSTER_BASE
```

## 生成配置文件

```bash
function generate_instance_conf() {
  echo "configuring server $1"
  
  # clean conf file
  echo "" > $1/redis.conf
  # write conf
  echo "port $1" >> $1/redis.conf
  echo "# bind 0.0.0.0" >> $1/redis.conf
  echo "dir $REDIS_CLUSTER_BASE/$port/data" >> $1/redis.conf
  echo "cluster-enabled yes" >> $1/redis.conf
  echo "cluster-config-file nodes-$1.conf" >> $1/redis.conf
  echo "cluster-node-timeout 5000" >> $1/redis.conf
  echo "cluster-announce-ip 127.0.0.1" >> $1/redis.conf
  
  echo "appendonly yes" >> $1/redis.conf
  echo "daemonize yes" >> $1/redis.conf
}


# mkdir dirs and setup startup.sh
echo "#!/bin/bash" > $START_UP
servers=
for port in $PORTS; do 
  mkdir -p $REDIS_CLUSTER_BASE/$port/data
  # generate conf files
  generate_instance_conf $port
  # 
  echo "$REDIS_SERVER_BIN $REDIS_CLUSTER_BASE/$port/redis.conf" >> $START_UP
  # servers
  servers="$servers 127.0.0.1:$port "
done
```

最重要的就是这一步，因为以后还要搭建分布式环境所以没有绑定ip，其他可以参考redis/redis.conf解释。

## 开始命令文件

```bash
chmod +x $START_UP
echo "starting servers..."
$START_UP
sleep 5s
echo "servers ready!"

# create cluster
echo "configuring cluster..."
$REDIS_CLI_BIN --cluster create $servers --cluster-replicas 1
echo "configured!"
```

将启动命令写到新文件中并启动。

## 全文件

```bash
#!/bin/bash

source /home/k8s/homie/tools/0_parameters.sh
START_UP=$REDIS_CLUSTER_BASE/startup.sh
# Check redis command
if [ ! -f $REDIS_SERVER_BIN ]; then
  echo "Redis not ready, please install redis firstly!"
  echo ""
  echo "===== Install redis as follows ====="
  cd $BASE_DIR
  wget http://download.redis.io/releases/redis-6.2.2.tar.gz
  tar -zxvf ./redis-6.2.2.tar.gz
  mv redis-6.2.2 redis
  cd redis
  make
  cd $BASE_DIR
  echo ""
  echo "Redis Server Installation Finished!"
fi

# 一节点操作多个节点
# selectNode() {
#     nodenum=$1
#     specify_node=$2
#     n_l=(${NODELIST})
#     node=${n_l[@]:0:$nodenum}
#     if [ -n "${specify_node}" ]; then
#         node=${specify_node}
#     fi
#     echo ${node[@]}
# }

# # Remove redis cluster
# stopCluster() {
#     nodenum=$1
#     nodelist=$(selectNode ${nodenum})
#     for n in ${nodelist}; do
#         ssh $n "ps -ef | grep redis-server | grep cluster | awk '{print $2}' | xargs kill -9 "
#         echo ${n}
#         ssh $n "rm -rf "$REDIS_CLUSTER_BASE"/*;"
#     done

# }

# if [ "$1" = "--remove" ]; then
#   stopCluster $2
#   exit 0
# fi

# if [ "$1" = "--help" ]; then
#     echo "--remove nodenum" 
#     exit 0
# fi

# Remove redis cluster
function remove_cluster() {
  # kill redis servers
  ps -ef | grep redis-server | grep cluster | awk '{print $2}' | xargs kill -9 
  # disable systemd
  # systemctl disable redis-cluster.service
  # rm cluster data
  if [ -d "$REDIS_CLUSTER_BASE" ]; then
    rm -rf $REDIS_CLUSTER_BASE
  fi
}

if [ "$1" = "--remove" ]; then
  remove_cluster  
  exit 0
fi

# User custom setting
# echo -n "Enter your host's public address(default 127.0.0.1):"
# read cluster_address


# enter work directory
mkdir -p $REDIS_CLUSTER_BASE
cd $REDIS_CLUSTER_BASE

# generate configuration files
  # copy of redis.conf
# function generate_instance_conf() {
#   echo "configuring server $1"

#   cp $BASE_DIR/redis/redis.conf $REDIS_CLUSTER_BASE/config/redis-$1.conf
#   cd $REDIS_CLUSTER_BASE/config
#   sed  -i "s@# cluster-config-file nodes-6379.conf@cluster-config-file \"cluster-config-file$REDIS_CLUSTER_BASE/node/redis-$1.conf\"@g" redis-$1.conf
#   sed  -i "s/daemonize no/daemonize yes/g" redis-$1.conf
#   sed  -i "/protected-mode yes/d" redis-$1.conf
#   sed  -i "s/bind 127.0.0.1 -::1/# bind 127.0.0.1 -::1/g" redis-$1.conf 
#   sed  -i "s/port 6379/port $1/g" redis-$1.conf
#   sed  -i "s#pidfile /var/run/redis_6379.pid#pidfile /var/run/redis_$1.pid#g" redis-$1.conf
#   sed  -i "s#logfile \"\"#logfile \"$REDIS_CLUSTER_BASE/log/redis-$1.log\"#g" redis-$1.conf
#   sed  -i "s#dir ./#dir $REDIS_CLUSTER_BASE/data#g" redis-$1.conf
#   sed  -i "s/dbfilename dump.rdb/dbfilename dump-$1.rdb/g" redis-$1.conf
#   sed  -i "s/appendonly no/appendonly yes/g" redis-$1.conf
#   sed  -i "s/appendfilename \"appendonly.aof\"/appendfilename \"appendonly-$1.aof\"/g" redis-$1.conf
#   sed  -i "s/# cluster-enabled yes/cluster-enabled yes/g" redis-$1.conf
#   sed  -i "s@# cluster-config-file nodes-6379.conf@cluster-config-file \"$REDIS_CLUSTER_BASE/node/nodes-7000.conf\"@g" redis-$1.conf
# }


# # mkdir dirs and setup startup.sh with copy of redis.conf
# echo "#!/bin/bash" > $START_UP
# servers=
# for port in $PORTS; do 
#   # generate conf files
#   generate_instance_conf $port
#   # 
#   echo "$REDIS_SERVER_BIN $REDIS_CLUSTER_BASE/config/redis-$port.conf" >> $START_UP
#   # servers
#   servers="$servers 127.0.0.1:$port "
# done

# generate configuration files
function generate_instance_conf() {
  echo "configuring server $1"
  
  # clean conf file
  echo "" > $1/redis.conf
  # write conf
  echo "port $1" >> $1/redis.conf
  echo "# bind 0.0.0.0" >> $1/redis.conf
  echo "dir $REDIS_CLUSTER_BASE/$port/data" >> $1/redis.conf
  echo "cluster-enabled yes" >> $1/redis.conf
  echo "cluster-config-file nodes-$1.conf" >> $1/redis.conf
  echo "cluster-node-timeout 5000" >> $1/redis.conf
  echo "cluster-announce-ip 127.0.0.1" >> $1/redis.conf
  
  echo "appendonly yes" >> $1/redis.conf
  echo "daemonize yes" >> $1/redis.conf
}


# mkdir dirs and setup startup.sh
echo "#!/bin/bash" > $START_UP
servers=
for port in $PORTS; do 
  mkdir -p $REDIS_CLUSTER_BASE/$port/data
  # generate conf files
  generate_instance_conf $port
  # 
  echo "$REDIS_SERVER_BIN $REDIS_CLUSTER_BASE/$port/redis.conf" >> $START_UP
  # servers
  servers="$servers 127.0.0.1:$port "
done



# startup instances
chmod +x $START_UP
echo "starting servers..."
$START_UP
sleep 5s
echo "servers ready!"

# create cluster
echo "configuring cluster..."
$REDIS_CLI_BIN --cluster create $servers --cluster-replicas 1
echo "configured!"

# generate redis-cluster service file开机自启动
# cat << EOT > $REDIS_CLUSTER_BASE/redis-cluster.service
# [Unit]
# Description=Redis 5.0 Cluster Service
# After=network.target

# [Service]
# Type=forking
# ExecStart=/usr/local/redis-cluster/startup.sh

# [Install]
# WantedBy=default.target
# EOT

# # create service
# echo "Creating redis cluster service..."
# ln -s $REDIS_CLUSTER_BASE/$SERVICE /etc/systemd/system/$SERVICE 
# sudo systemctl daemon-reload && sudo systemctl enable $SERVICE && sudo systemctl start $SERVICE

# Cluster OK
echo ""
echo "Completed!"
echo ""
echo "Test cluster with: $REDIS_CLI_BIN -h 127.0.0.1 -p 7000"
echo ""
echo "127.0.0.1:7000>cluster nodes"

```

