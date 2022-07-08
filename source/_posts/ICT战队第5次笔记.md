---
title: ICT战队-第5次笔记
subtitle: 
summary: 静态路由 OSPF协议
description: 静态路由 OSPF协议
keyworks: 
author: hhhhmdzz
language: zh-Hans
timezone: Asia/Shanghai
date: 2022-06-29 10:50:01
categories: 
- ICT战队数据通信
tags: 
- HCIA
- 华为eNSP
mathjax: true
---

# ICT战队-第5次笔记

[toc]

## 路由协议

### **实验一：静态路由**

- STEP1：网络拓扑

<img src="./2f4fbfc4c48246bcb81ce7296075cf04.png">

- STEP2.1：路由器配置IP

说明：`AR几`就配置`X.X.X.几/24`的地址，例：AR2：12.1.5.2/24。

```shell
# AR1
[AR1]int g0/0/0
[AR1-GigabitEthernet0/0/0]ip add 192.168.1.1 24
[AR1-GigabitEthernet0/0/0]int g0/0/1
[AR1-GigabitEthernet0/0/1]ip add 12.1.1.1 24

# AR2
[AR2]int g0/0/0
[AR2-GigabitEthernet0/0/0]ip add 12.1.1.2 24
[AR2-GigabitEthernet0/0/0]int g0/0/1
[AR2-GigabitEthernet0/0/1]ip add 23.1.1.2 24

# AR3
[AR3]int g0/0/0
[AR3-GigabitEthernet0/0/0]ip add 23.1.1.3 24
[AR3-GigabitEthernet0/0/0]int g0/0/1
[AR3-GigabitEthernet0/0/1]ip add 192.168.2.3 24
```

- STEP2.2：主机配置IP

| 主机 |    IP地址     |     掩码      |    网关     |
| :--: | :-----------: | :-----------: | :---------: |
| PC1  | 192.168.1.250 | 255.255.255.0 | 192.168.1.1 |
| PC2  | 192.168.2.250 | 255.255.255.0 | 192.168.2.3 |

- STEP2.3：测试网络连通性(略)

用PC1 ping PC2，ping不通。

- STEP2.4：查看路由表

```shell
[AR2]dis ip rou
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 10       Routes : 10       

Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface

       12.1.1.0/24  Direct  0    0           D   12.1.1.2        GigabitEthernet0/0/0
       12.1.1.2/32  Direct  0    0           D   127.0.0.1       GigabitEthernet0/0/0
     12.1.1.255/32  Direct  0    0           D   127.0.0.1       GigabitEthernet0/0/0
       23.1.1.0/24  Direct  0    0           D   23.1.1.2        GigabitEthernet0/0/1
       23.1.1.2/32  Direct  0    0           D   127.0.0.1       GigabitEthernet0/0/1
     23.1.1.255/32  Direct  0    0           D   127.0.0.1       GigabitEthernet0/0/1
      127.0.0.0/8   Direct  0    0           D   127.0.0.1       InLoopBack0
      127.0.0.1/32  Direct  0    0           D   127.0.0.1       InLoopBack0
127.255.255.255/32  Direct  0    0           D   127.0.0.1       InLoopBack0
255.255.255.255/32  Direct  0    0           D   127.0.0.1       InLoopBack0

[AR2]
```

- STEP2.5：DEBUG

在理想环境下，抓包容易，但是在真实生产环境中，抓包很难实现。因此，一般使用DEBUG写日志。

<font color=#999AAA>让客户主动找你，别主动去找客户。</font>

```shell
# todo 显示日志
<AR2>terminal monitor  # 在终端显示日志
Info: Current terminal monitor is on.
<AR2>terminal debugging  # 显示日志等级
Info: Current terminal debugging is on.
# <AR2>terminal all  # 永远不要使用该命令
# todo 显示相应日志
<AR2>debugging ip icmp
```

然后，使用AR1 ping AR2，可以在AR2终端看到(ping通了)：

```shell
# todo AR2终端信息
<AR2>
Oct 12 2021 17:46:11.832.2-08:00 AR2 IP/7/debug_icmp:
ICMP Send: echo-reply(Type=0, Code=0), Src = 2.1.1.12, Dst = 1.1.1.12, ICMP Id = 0xabcd, ICMP Seq = 256

<AR2>
Oct 12 2021 17:46:12.272.1-08:00 AR2 IP/7/debug_icmp:
ICMP Receive: echo(Type=2048, Code=0), Src = 1.1.1.12, Dst = 2.1.1.12, ICMP Id = 0xabcd, ICMP Seq = 512

<AR2>
Oct 12 2021 17:46:12.272.2-08:00 AR2 IP/7/debug_icmp:
ICMP Send: echo-reply(Type=0, Code=0), Src = 2.1.1.12, Dst = 1.1.1.12, ICMP Id = 0xabcd, ICMP Seq = 512

<AR2>
Oct 12 2021 17:46:12.772.1-08:00 AR2 IP/7/debug_icmp:
ICMP Receive: echo(Type=2048, Code=0), Src = 1.1.1.12, Dst = 2.1.1.12, ICMP Id = 0xabcd, ICMP Seq = 768

<AR2>
Oct 12 2021 17:46:12.772.2-08:00 AR2 IP/7/debug_icmp:
ICMP Send: echo-reply(Type=0, Code=0), Src = 2.1.1.12, Dst = 1.1.1.12, ICMP Id = 0xabcd, ICMP Seq = 768

<AR2>
Oct 12 2021 17:46:13.272.1-08:00 AR2 IP/7/debug_icmp:
ICMP Receive: echo(Type=2048, Code=0), Src = 1.1.1.12, Dst = 2.1.1.12, ICMP Id = 0xabcd, ICMP Seq = 1024

<AR2>
Oct 12 2021 17:46:13.272.2-08:00 AR2 IP/7/debug_icmp:
ICMP Send: echo-reply(Type=0, Code=0), Src = 2.1.1.12, Dst = 1.1.1.12, ICMP Id = 0xabcd, ICMP Seq = 1024

<AR2>
Oct 12 2021 17:46:13.762.1-08:00 AR2 IP/7/debug_icmp:
ICMP Receive: echo(Type=2048, Code=0), Src = 1.1.1.12, Dst = 2.1.1.12, ICMP Id = 0xabcd, ICMP Seq = 1280

<AR2>
Oct 12 2021 17:46:13.762.2-08:00 AR2 IP/7/debug_icmp:
ICMP Send: echo-reply(Type=0, Code=0), Src = 2.1.1.12, Dst = 1.1.1.12, ICMP Id = 0xabcd, ICMP Seq = 1280

<AR2>
```

[ICMP类型字段(Type)以及代码字段(Code)含义汇总](https://blog.csdn.net/zhenyi2000/article/details/79795095)

- STEP3.1：配置静态路由

静态路由配置语法：`[AR]ip route-static <目标网段> <子网掩码> <下跳地址>`

```shell
# todo AR1配置静态路由
[AR1]ip route-static 192.168.2.0 255.255.255.0 12.1.1.2

# todo AR2配置静态路由
[AR2]ip route-static 192.168.2.0 255.255.255.0 23.1.1.3
[AR2]ip route-static 192.168.1.0 255.255.255.0 12.1.1.1

# todo AR3配置静态路由
[AR3]ip route-static 192.168.1.0 255.255.255.0 23.1.1.2
```

- STEP3.2：测试网络连通性

主机之间可以ping通，其通信过程为：

<img src="./33d9c319820d41ddb02fabbe4654a383.png">

**路由器基于路由表进行转发**。

### 小实验$_1$

两个路由器地址不在同一网段能否通信。

- STEP1：网络拓扑

<img src="./62af52d773c54532bb3cd64d843be2e8.png">

- STEP2：路由器配置IP(略)
- STEP3：测试连通性(略：路由器互相ping不通)
- STEP4：添加静态路由

```shell
# todo AR1
[AR1]ip route-static 192.168.1.1 255.255.255.0 g0/0/0
Info: The destination address and mask of the configured static route mismatched
, and the static route 192.168.1.0/24 was generated.

# todo AR2
[AR2]ip route-static 12.1.1.1 255.255.255.0 g0/0/0
Info: The destination address and mask of the configured static route mismatched
, and the static route 12.1.1.0/24 was generated.
```

- STEP5：测试连通性

此时，**按理来说**，应该能ping通了。
但是，涉及到ARP轮询技术(?)，没有ping通。

>注；如果路由器没有基于路由表进行转发，一般就是路由器出BUG了。

### **原理：路由协议**

<img src="./ac940265ba7d419bb08b350e0f188a80.png">

查询AS：[CIDR Report](https://www.cidr-report.org/as2.0/)

| 算法                 | 协议       |
| -------------------- | ---------- |
| 距离矢量路由选择协议 | RIP、BGP   |
| 链路状态路由选择协议 | OSPF、ISIS |

距离矢量：实时根据方向的距离转发数据
链路状态：已经计算好转发数据的最短路径

路由度量：

<img src="./1849c73748a74dba92878c6f96ce3003.png">

等价路由：ECMP

<img src="./b8f533e8554148be93a4b4cb5f58f219.png">

例：等价路由

<img src="./c74f445e32564661b7d857b3d856a1d6.png">

路由表**最长匹配原则**

VRP缺省路由优先级：

| 路由协议 | 优先级 |
| :------: | :----: |
|  DIRECT  |   0    |
|   OSPF   |   10   |
|  IS-IS   |   15   |
|  STATIC  |   60   |
|   RIP    |  100   |
| OSPF ASE |  150   |

### 小实验$_2$

- STEP1：网络拓扑

<img src="./8aaf15f88feb46068eec0ef6c6ba23f3.png">

- STEP2.1：路由器配置环回口地址

路由器的环回口可以自己指定，配置任意IP地址。

```shell
# todo AR1：配置环回口
[AR1]int LoopBack 0
[AR1-LoopBack0]ip add 1.1.1.1 32

# todo 查看环回地址
[AR1]dis ip rou
[AR1]dis ip int bri
```

- STEP2.2：配置IP地址

```shell
# todo AR1：配置IP
[AR1int g0/0/0
[AR1-GigabitEthernet0/0/0]ip add 12.1.1.1 24
```

```shell
# todo AR2：配置IP
[AR2]int g0/0/0
[AR2-GigabitEthernet0/0/0]ip add 12.1.1.2 24
[AR2-GigabitEthernet0/0/0]int g0/0/1
[AR2-GigabitEthernet0/0/1]ip add 192.168.1.2 24
```

| 主机 | IP地址        | 子网掩码      | 网关        |
| ---- | ------------- | ------------- | ----------- |
| PC1  | 192.168.1.250 | 255.255.255.0 | 192.168.1.2 |

- STEP2.3：配置默认路由

末梢网络一般添加默认路由

```shell
# todo AR1：配置默认路由
[AR1]ip route-static 192.168.1.0 255.255.255.0 12.1.1.2
```

```shell
# todo AR2：配置默认路由
[AR2]ip route-static 0.0.0.0 0.0.0.0 12.1.1.1
```

- STEP0：测试连通

```shell
# tood AR1：
[AR1]ping -a 1.1.1.1 192.168.1.250  
  PING 192.168.1.250: 56  data bytes, press CTRL_C to break
    Request time out
    Reply from 192.168.1.250: bytes=56 Sequence=2 ttl=127 time=40 ms
    Reply from 192.168.1.250: bytes=56 Sequence=3 ttl=127 time=30 ms
    Reply from 192.168.1.250: bytes=56 Sequence=4 ttl=127 time=30 ms
    Reply from 192.168.1.250: bytes=56 Sequence=5 ttl=127 time=20 ms

  --- 192.168.1.250 ping statistics ---
    5 packet(s) transmitted
    4 packet(s) received
    20.00% packet loss
    round-trip min/avg/max = 20/30/40 ms

[AR1]
```

- STEP3：删除主机连线，路由器之间成环

<img src="./3d43052de942494c9a89739643363d1a.png">

此时测试连通性：

```shell
[AR1]tracert -a 1.1.1.1 192.168.1.250
 traceroute to  192.168.1.250(192.168.1.250), max hops: 30 ,packet length: 40,press CTRL_C to break 
 1 12.1.1.2 50 ms  20 ms  20 ms 
 2 12.1.1.1 10 ms  20 ms  10 ms 
 3 12.1.1.2 20 ms  10 ms  30 ms 
 4 12.1.1.1 20 ms  20 ms  20 ms 
······
27 12.1.1.2 80 ms  60 ms  100 ms 
28 12.1.1.1 70 ms  80 ms  80 ms 
29 12.1.1.2 70 ms  100 ms  80 ms 
30 12.1.1.1 80 ms  90 ms  80 ms 
[AR1]
```

环路原因：路由器端口down了后，其路由表中的对应的路由条目会被删除，此时路由器就会根据默认路由进行转发，这两台路由器都根据默认路由进行转发就形成了环路。

- STEP4：配置防环的丢弃路由

丢弃路由：匹配到指定的地址都丢弃掉

```shell
# todo AR2：配置丢弃路由
[AR2]ip route-static 192.168.1.0 255.255.255.0 NULL 0
```

- STEP0：分别连接/断开连线测试连通性

```shell
# todo AR2：线路连接时的路由表
[AR2]dis ip rou
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 11       Routes : 11       

Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface

        0.0.0.0/0   Static  60   0          RD   12.1.1.1        GigabitEthernet0/0/0
       12.1.1.0/24  Direct  0    0           D   12.1.1.2        GigabitEthernet0/0/0
       12.1.1.2/32  Direct  0    0           D   127.0.0.1       GigabitEthernet0/0/0
    ······
    192.168.1.0/24  Direct  0    0           D   192.168.1.2     GigabitEthernet0/0/1
    192.168.1.2/32  Direct  0    0           D   127.0.0.1       GigabitEthernet0/0/1
  192.168.1.255/32  Direct  0    0           D   127.0.0.1       GigabitEthernet0/0/1
255.255.255.255/32  Direct  0    0           D   127.0.0.1       InLoopBack0

# todo AR2：线路断开时的路由表
[AR2]dis ip rou
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 9        Routes : 9        

Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface

        0.0.0.0/0   Static  60   0          RD   12.1.1.1        GigabitEthernet0/0/0
       12.1.1.0/24  Direct  0    0           D   12.1.1.2        GigabitEthernet0/0/0
       12.1.1.2/32  Direct  0    0           D   127.0.0.1       GigabitEthernet0/0/0
    ······
    192.168.1.0/24  Static  60   0           D   0.0.0.0         NULL0
255.255.255.255/32  Direct  0    0           D   127.0.0.1       InLoopBack0
```

注意到：
连接：`192.168.1.0/24  Direct  0    0           D   192.168.1.2     GigabitEthernet0/0/1`
断开：`192.168.1.0/24  Static  60   0           D   0.0.0.0         NULL0`

- 总结
  - 路由：RIP、OSPG、BGP
  - 动态协议：距离矢量、链路状态
  - 路由表：目的地址、协议、优先级、度量、下一跳、下一跳端口
  - 等价路由：同一协议，到同一目的有多个相同度量

## 路由

### 静态路由

<font color=#999AAA>略：上面已经做过</font>

```shell
# todo 命令：配置到达某个网段的静态路由
[AR]ip route-static <目标网段> <子网掩码> <出口地址>
[AR]ip route-static <目标网段> <子网掩码位数> <下一跳地址>
[AR]ip route-static <目标网段> <子网掩码位数> <下一跳端口>
```

### 负载分担

```shell
# todo 命令：配置等价路由实现负载分担
[AR]ip route-static <目标网段> <子网掩码> <出口地址1>
[AR]ip route-static <目标网段> <子网掩码> <出口地址2>
[AR]ip route-static <目标网段> <子网掩码> <出口地址3>
```

### 路由备份

```shell
# todo 命令：静态路由的路由备份
[AR]ip route-static <目标网段> <子网掩码> <出口地址1>
[AR]ip route-static <目标网段> <子网掩码> <出口地址2> preference 100
```

<img src="./9a6bb1b37b814f4f9ad645286c1befb7.png">

### 缺省路由

```shell
# todo 命令：配置缺省路由
[AR]ip route-static 0.0.0.0 0.0.0.0 <下一跳地址>
```

## OSPF路由协议

### **原理：OSPF路由协议**

- **①开方式最短路径优先协议OSPF**

<img src="./a7cc17215aba44b2a7f5d4bc5b7b1c34.png">

协议号：[IP协议(Protocol)字段释义](https://blog.csdn.net/zhenyi2000/article/details/79795140)

有些特定**防火墙过滤**某一协议得写协议号。

- **②OSPF基本特点**

<img src="./d9775633684f4fc68de933983ab55079.png">

无类：不关心IP地址的类别
区域划分：骨干、非骨干区域(非骨干必须和骨干直连)
无路由自环：迪杰斯特拉
收敛快：毫秒级别
组播：代替广播，使用224.0.0.5和224.0.0.6
等值路由：负载均衡
认证：支持认证

- **③OSPF报文**

OSPF的3张表：邻居表、链路状态数据库、路由表

OSPF五种报文：[OSPF报文类型及格式总结](https://blog.csdn.net/qq_43499889/article/details/104987294)
<font color=#999AAA>LSA( Link-State Advertisement )链路状态通告</font>

1. Hello报文：用来发现并维持邻居
2. DD报文(Database Description)：用来描述本地LSDB信息(和邻居是否互通)，只携带LSA的Header
3. LSR报文(Link State Request，链路状态请求)：用于请求邻居的LSA
4. LSU报文()：发送LSA具体信息，或者用于泛洪LSA，或者用于隐式回应LSU报文
5. LSAck报文()：用于回应LSU报文

- **④OSPF状态过程**

**Init**：已经把Hello报文发出去，但是暂没收到回应，默认10s发一次，如果收到回复，则进入 2-way 状态
**2-way**：选举DR和BDR，2-way这个状态最长持续40s。
**ExStart**：
**Exchange**：
**Loading**：
**Full**：

- **⑤DR和BDR**

避免一个更新被重复传递多次，减少路由更新消耗的资源。

DRother只能和DR、BDR建立邻接关系，DRother只能建立 2-way 状态

选DR和BDR：比较RouterID(RID)大小(越大越优)

**RID来源**：
1.管理员手动设置
2.活动的环回口最大IP
3.活动的物理接口最大IP

在Wireshark中，OSPF数据帧的`Source OSPF Router`就是RID

OSPF先选举BDR：比大小，谁大谁先成为BDR，如果没有DR，BDR就变成DR，然后再选举BDR。

显示确认：使用专有的报文进行确认
**隐式确认**：(基于**序列号**的确认机制)收到一份报文，然后向发送者回复一份**序列号相同**的报文。仅有OSPF支持这种确认机制。

### **实验二：使用debug观察邻接关系形成过程**

- STEP1：网络拓扑

<img src="./332dc88ff0414092aa2b22ee4142ebcf.png">

- STEP2：查看OSPF表

首先打开抓包

然后清空邻居关系

```shell
<R1>reset ospf process 
Warning: The OSPF process will be reset. Continue? [Y/N]:y
```

查看抓到的数据包/查询路由表State：

```shell
# todo 查看OSPF邻居表
[R1]dis ospf peer

         OSPF Process 1 with Router ID 12.1.1.1
                 Neighbors 

 Area 0.0.0.0 interface 12.1.1.1(GigabitEthernet0/0/0)'s neighbors
 Router ID: 12.1.1.2         Address: 12.1.1.2        
   State: Full  Mode:Nbr is  Master  Priority: 1
   DR: 12.1.1.2  BDR: 12.1.1.1  MTU: 0    
   Dead timer due in 31  sec 
   Retrans timer interval: 5 
   Neighbor is up for 00:03:03     
   Authentication Sequence: [ 0 ] 

[R1]
```

### **实验三：DR和BDR**

- STEP1：网络拓扑

<img src="./d946c30a16fc44918cc8e3406bbfdd3b.png">

- STEP2：配置IP地址(略)

除了网络拓扑中的端口IP地址，还有：

| 路由器 | 环回地址 |
| ------ | -------- |
| AR1    | 10.0.1.1 |
| AR2    | 10.0.2.2 |
| AR3    | 10.0.3.3 |

- STEP3：配置OSPF

OSPF宣告网段：`network <目标网段> <反掩码>`

```shell
# todo AR3：配置OSPF
[R3]ospf  # OSPF进程ID
[R3-ospf-1]a 0  # 进入区域
[R3-ospf-1-area-0.0.0.0]network 10.0.123.0 0.0.0.255  # 宣告直连网段
[R3-ospf-1-area-0.0.0.0]network 10.0.3.0 0.0.0.255  # 宣告换回网段
# [R3-ospf-1]silent-interface LoopBack 0  # 如果需要，启用抑制端口
```

```shell
# todo AR1：配置OSPF
[R1]ospf
[R1-ospf-1]a 0
[R1-ospf-1-area-0.0.0.0]network 10.0.123.0 0.0.0.255
······(可以看到：Down->Init->2Way->ExStart->Exchange->Loading)
[R1-ospf-1-area-0.0.0.0]network 10.0.1.0 0.0.0.255
```

```shell
# todo AR2：配置OSPF
[R2]ospf
[R2-ospf-1]a 0.0.0.0
[R2-ospf-1-area-0.0.0.0]network 10.0.123.0 0.0.0.255
[R2-ospf-1-area-0.0.0.0]network 10.0.2.0 0.0.0.255
```

>问：直连路由还有必要宣告吗？
>答：如果不宣告，那么该接口无法收发OSPF报文。

`network`功能：1.宣告网段，2.接口收发OSPF报文

如果路由器端口连接了终端PC，建议配置好`network`后，再把接口功能过滤掉，即抑制端口`silent-interface`：只宣告网段，但不能收发OSPF报文。(没必要和PC收发OSPF报文)

- STEP4：查看DR和BDR

```shell
[AR]dis ospf peer  # 查看OSPF邻居关系
```

在这里，因为我们路由器配置顺序为：AR3->AR1->AR2
因为没开启抢占特性，所以AR3为DR，AR1为BDR，AR2为DROther

每隔10s发送一个Hello报文，40s收不到Hello，断开邻居关系(死亡间隔40s)

### **实验四：OSPF认证**

- STEP1：网络拓扑(略：同上)
- STEP2：配置认证

```shell
# todo AR1、AR2、AR3均配置认证
[R1-GigabitEthernet0/0/0]ospf authentication-mode md5 1 cipher huawei
# md5方式加密，Ket id为1，Key id保持一致！
```

hmac-md5：[HMAC-MD5 签名算法](https://blog.csdn.net/chy2z/article/details/80828770)
simple：防君子不妨小人，认证之后正常收发OSPF包，但是抓包能直接看到密码

修改**卡信息：网络验证：只能改服务器数据；本地验证：想咋改咋改(不会...)

>技巧：每个文件都会有一个自己的指纹信息，生成一个32位/64位的哈希值，且该值唯一，扫描文件的MD5值，只要发现有与违法视频/文件MD5值匹配的，就认为扫描的文件违法。所以，如果想在百度网盘上传一个不健康的小视频/软件，那么可以把一个非空的.txt文件和想要上传的文件一起压缩，再上传即可。

<font color=#999AAA>饱汉不知饿汉饥:smirk:</font>

### **实验五：多区域OSPF**

- STEP1：网络拓扑

<img src="./169b2c3f83ec4f608e592b6b3adcfaef.png">

- STEP2：配置多区域OSPF

```shell
# todo AR3：配置多区域OSPF
[R3]int g0/0/1
[R3-GigabitEthernet0/0/1]ip add 10.0.34.3 24  # 接口添加IP地址

[R3]ospf 1
[R3-ospf-1]a 1  # 进入区域1
[R3-ospf-1-area-0.0.0.1]network 10.0.34.0 0.0.0.255  # 宣告区域
```

| 路由器 | IP地址    | 环回地址 |
| ------ | --------- | -------- |
| AR4    | 10.0.34.4 | 10.0.4.4 |

```shell
# todo AR4：配置多区域OSPF
[AR4]int g0/0/0
[AR4-GigabitEthernet0/0/0]ip add 10.0.34.4 24  # 接口配置地址
[AR4-GigabitEthernet0/0/0]int loo 0
[AR4-LoopBack0]ip add 10.0.4.4 24  # 环回口网络类型：点到点，掩码按32宣告

[R4]ospf 1
[R4-ospf-1]a 1  # 进入区域1
[R4-ospf-1-area-0.0.0.1]network 10.0.34.0 0.0.0.255  # 宣告网段

[R4-ospf-1-area-0.0.0.1]int loo 0
[AR4-LoopBack0]ospf enable 1 a 1
# 宣告环回口网段的一种方式，和在OSPF area里面用network效果一样
```

```shell
[AR4-ospf-1-area-0.0.0.1]network 10.0.34.4 0.0.0.0
# 宣告端口，虽然反掩码是32，但是还是按端口反掩码进行正常宣告
```

(理论上不允许！)物理上非骨干区域连接非骨干区域，可以想办法把两个区域变成一个区域：Virtual Link。

- 练习题：OSPF故障排查
