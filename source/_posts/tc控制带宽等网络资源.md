---
title: tc控制带宽等网络资源
top: false
cover: false
toc: true
mathjax: true
tags:
  - 学习
categories:
  - 网络
typora-root-url: ../
date: 2022-04-01 16:26:33
password:
summary:
---

## 限制单个网卡带宽

### 使用 TC 下载限制单个 IP 进行速度控制

```bash
# 删除原有规则
sudo tc qdisc del dev ens4f1 root
sudo tc qdisc add dev ens4f1 root handle 1: htb r2q 1
sudo tc class add dev ens4f1 parent 1: classid 1:1 htb rate 20mbit ceil 25mbit
sudo tc  filter add dev ens4f1 parent 1: protocol ip prio 16 u32 match ip dst 10.0.0.62  flowid 1:1
```

就可以限制 192.168.1.2 的下载速度为 30Mbit 最高可以 60Mbit ,其中 r2q,是指没有 default 的root,使整个网络的带宽没有限制

### 使用 TC 对整段 IP 进行速度控制 

```bash
tc qdisc add dev eth0 root handle 1: htb r2q 1 tc class add dev
eth0 parent 1: classid 1:1 htb rate 50mbit ceil 1000mbit 
tc filter add dev eth0 parent 1: protocol ip prio 16 u32 match ip dst 192.168.111.0/24 flowid 1:1
```

就可以限制 192.168.111.0 到 255 的带宽为 3000k 了,实际下载速度为 200k 左右。这种情况下,这个网段所有机器共享这 200k 的带宽。

### 加入一个 sfq(随机公平队列)

```javascript
tc qdisc add dev eth0 root handle 1: htb r2q 1 
tc class add dev eth0 parent 1: classid 1:1 htb rate 3000kbit burst 10k 
tc qdisc add dev eth0 parent 1:1 handle 10: sfq perturb 10 
tc filter add dev eth0 parent 1: protocol ip prio 16 u32 match ip dst 192.168.111.168 flowid 1:1
```

sfq可以防止一个段内的一个 ip 占用整个带宽。使用 TC 控制服务器对外的速度为 10M

## 模拟网络抖动

```bash
tc qdisc add dev eth0 root netem delay 100ms
```

该命令将 eth0 网卡的传输设置为延迟 100 毫秒发送。

更真实的情况下,延迟值不会这么精确,会有一定的波动,我们可以用下面的情况来模拟出

### 带有波动性的延迟值:

```bash
tc qdisc add dev eth0 root netem delay 100ms 10ms
```

该命令将 eth0 网卡的传输设置为延迟 100ms ± 10ms (90 ~ 110 ms 之间的任意值)发送。

还可以更进一步加强这种波动的随机性:

```bash
tc qdisc add dev eth0 root netem delay 100ms 10ms 30%
```

该命令将 eth0 网卡的传输设置为 100ms ,同时,大约有 30% 的包会延迟 ± 10ms 发送。现在 ping 一下机器可以看出数据明显的波动性。

```
tc qdisc add dev eth0 root netem loss 1%
```

该命令将 eth0 网卡的传输设置为随机丢掉 1% 的数据包。示例:在 216 上执行

```
tc qdisc add dev eth0 root netem loss 10%
```

显示 16 个包只有 13 个收到了。也可以设置丢包的成功率:

```
# tc qdisc add dev eth0 root netem loss 1% 30%
```

该命令将 eth0 网卡的传输设置为随机丢掉 1% 的数据包,成功率为 30% 。

### 删除网卡上面的相关配置

将之前命令中的 add 改为 del 即可删除配置:

```
# tc qdisc del dev eth0 XXXXXXXXXXX
```

(自己加的配置)该命令将 删除 eth0 网卡的相关传输配置

至此,我们已经可以通过 TC 在测试环境中模拟一定的网络延时和丢包的情况。下面是关于tc 更多的应用和介绍

### 模拟包重复

```
 tc qdisc add dev eth0 root netem duplicate 1%
```

该命令将 eth0 网卡的传输设置为随机产生 1% 的重复数据包 。

### 模拟数据包损坏

```
tc qdisc add dev eth0 root netem corrupt 0.2%
```

该命令将 eth0 网卡的传输设置为随机产生 0.2% 的损坏的数据包 。 (内核版本需在 2.6.16 以上)

### 模拟数据包乱序

```
tc qdisc change dev eth0 root netem delay 10ms reorder 25% 50%
```

该命令将 eth0 网卡的传输设置为:有 25% 的数据包(50%相关)会被立即发送,其他的延迟10 秒。

新版本中,如下命令也会在一定程度上打乱发包的次序:# tc qdisc add dev eth0 root netem delay 100ms 10ms

## 查看已经配置的网络条件

```
tc qdisc show dev eth0
```

该命令将 查看并显示 eth0 网卡的相关传输配置

## 删除tc规则

tc qdisc del dev eth0 root

参考：

[linux下使用tc控制和模拟网络流量](https://cloud.tencent.com/developer/article/1772836?from=15425)
