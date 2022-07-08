---
title: ICT战队-第9次笔记
subtitle: 
summary: BGP边界网关协议
description: BGP边界网关协议
keyworks: 
author: hhhhmdzz
language: zh-Hans
timezone: Asia/Shanghai
date: 2022-06-29 10:50:18
categories: 
- ICT战队数据通信
tags: 
- HCIA
- 华为eNSP
mathjax: true
---

# ICT战队-第9次笔记

[toc]

## BGP

### **原理：BGP自治系统间的路由协议**

BGP外部路由协议，是一种增强的距离矢量路由协议。

可以根据链路状态数据库画出整个网络拓扑图。

BGP边界网关协议，用于AS与AS之间的路由协议

BGP：支持CIDR
传输协议：**TCP**，端口号：**179**
无需周期性更新
路由更新：只发送增量路由
周期性发送keepAlive报文检测TCP连通性

BGP报文(5种)：

- Open包：负责和对等体peer建立邻居关系。(**建立**)
- KeepAlive：该消息在对等体之间周期性地发送，用以维护连接。(**维护**)
- Update：该消息被用来在BGP对等体之间传递路由信息。(**更新**)
- Notification：当 BGP Speaker检测到错误的时候，就发送该消息给对等体。(**纠错**)
- Route-refresh：用来通知对等体自己支持路由刷新能力。

BGP路由传递

EBGP水平分割：经过那个AS，反过来不能再进入该AS
IBGP水平分割：通过IBGP学习到的路由条目反过来不能发给IBGP

AS号
公有AS：1 ~ 64511
私有AS：64512 ~ 65535

BGP两种邻居关系：IBGP、EBGP
当BGP被用在一个AS内部：IBGP
当BGP被用在AS之间：EBGP

BGP只能使用一个进程号

```shell
# todo AR1：展示BGP只能使用一个进程号
[R1]bgp 1
[R1-bgp]bgp 2
Error: BGP is already running. The AS is 1.
```

注：
1.BGP宣告路由信息必须和路由表一致
2.BGP建立邻居关系建议直接使用物理接口
3.BGP建立邻居关系：`peer [对方IP] as-number [AS号]`
4.BGP邻居关系建立30s-60s

### **实验一$_1$：BGP简单配置**

目的：AR2和AR1建立邻居关系，且AR1的环回地址在AR2路由表项中

- **STEP1：** 网络拓扑

打开老师的拓扑

<img src="./7a748725d42144b1aaa0027e44e0fec9.png">

>说明：
>1.两个路由器间的网段分别是：`12.1.1.0`、`23.1.1.0`、`34.1.1.0`、`64.1.1.0`
>2.每个路由器上面都配了环回地址

- **STEP2.0：** BGP建立邻居关系

BGP邻居关系通过**单播**建立，所以需要**手动**指定邻居

`peer 对方接口IP地址 as-number 16`
对方AS号：16

可以先在AR1和AR2之间开启抓包

```shell
# todo AR1：为对等体配置AS号
[R1]bgp 1
[R1-bgp]peer 12.1.1.2 as-number 16

# todo AR2：为对等体配置AS号
[R2]bgp 16
[R2-bgp]peer 12.1.1.1 as-number 1
```

AR1进行BGP宣告网段之前，可以查看一下AR2的路由表，宣告之后再次查看即可看到AR2的路由表添加了AR1宣告网段的路由条目。

```shell
# show 错误写法：AR1：BGP宣告网段
[R1-bgp]network 1.1.1.1 24  # 必须按照路由表严格宣告！
Info: The network does not exist.
```

- **STEP2.1：** 看一下Notification报文(可以跳过这步)

```shell
# todo AR1：修改AS号
[R1-bgp]q
[R1]un bgp 1
Warning: All BGP configurations will be deleted. Continue? [Y/N]: y
[R1]bgp 1
[R1-bgp]peer 12.1.1.2 as-number 166  
```

在wireshark可以看到`NOTIFICATION`报文信息：

<img src="./3560064be56b44eebd13750a3a4d78f8.png">

```shell
# todo AR1：修改回原来的AS号
[R1-bgp]q
[R1]un bgp 1
Warning: All BGP configurations will be deleted. Continue? [Y/N]: y
[R1]bgp 1
[R1-bgp]peer 12.1.1.2 as-number 16
# 注意此时还没有进行BGP宣告路由！
```

- **STEP2.2：** 查看BGP邻居状态

```shell
# TODO AR1：查看BGP邻居状态
<R1>dis bgp peer
 BGP local router ID : 12.1.1.1
 Local AS number : 1
 Total number of peers : 1                Peers in established state : 1
  Peer            V          AS  MsgRcvd  MsgSent  OutQ  Up/Down       State PrefRcv
  12.1.1.2        4          16       14       14     0 00:12:45 Established       0
```

Connect说明正在进行TCP三次握手；Active和Connect之间来回切换则检查两个路由器之间单播是否可达

|  属性   | 说明                       |
| :-----: | -------------------------- |
|  Peer   | 邻居地址                   |
|    V    | 邻居IP版本                 |
|   AS    | 邻居AS号                   |
|  State  | 与邻居之间的链路状态       |
| PrefRcv | 从该邻居接收的路由信息条数 |

- **STEP2.3：** BGP宣告网段

```shell
# todo AR1：BGP宣告网段
[R1-bgp]network 1.1.1.1 32
```

- **STEP2.4：** 查看BGP邻居信息及其BGP学习到的路由信息

```shell
# todo AR2：查看BGP邻居
<R2>dis bgp peer
 BGP local router ID : 12.1.1.2
 Local AS number : 16
 Total number of peers : 1                Peers in established state : 1
  Peer            V          AS  MsgRcvd  MsgSent  OutQ  Up/Down       State PrefRcv
  12.1.1.1        4           1       27       27     0 00:24:20 Established       1
<R2>  # 可以看到从对方学习到一条路由条目
```

```shell
# todo AR2：查看BGP学习到的路由
<R2>dis bgp rou
 BGP Local router ID is 12.1.1.2 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 1
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>   1.1.1.1/32         12.1.1.1        0                     0      1i
```

| 符号 | 说明                |
| :--: | ------------------- |
|  *   | 表示下一跳有效/可达 |

>|表示指向的最优的路由条目(当有多条路由条目，选择最优的)
>(空格)|表示EBGP学习到的路由条目
>i|表示通过IBGP学习到的路由条目
>Path|路径(AS号)
>Ogn|起源属性

| Ogn  | 说明                   |
| :--: | ---------------------- |
|  ?   | 通过重分发学习到       |
|  i   | 通过BGP学习到          |
|  e   | 通过EGP(BGP前身)学习到 |

### **实验一$_2$：BGP更新源**

在上一个实验基础上继续

目的：让2、4环回口建立IGBP邻居关系

- **STEP3.0：** BGP建立邻居关系

BGP不需要配置中间路由器
IBGP指AS号，如果两个路由器都是AS 200，互相指向对方，AS号写200即可
一般IBGP建立邻居关系喜欢使用环回口，

```shell
# todo AR2：配置BGP对等体
[R2]bgp 16
[R2-bgp]peer 4.4.4.4 as-number 16

# todo AR4：配置BGP对等体
[R4]bgp 16
[R4-bgp]peer 2.2.2.2 as-number 16
```

此时，虽然配置了BGP，但是邻居关系无法建立

- **STEP3.1：** 抓包分析原因

在AR2和AR3之间的线路开始抓包，看到：

<img src="./761187adb6954ec98b767489c81572a4.png">

>现象：双方不断地进行TCP连接建立的请求和拒绝
>原因：源地址不是BGP建立邻居关系的地址，所以无法建立邻居关系

- **STEP3.2：** 配置更新源

IGBP跟对方建立连接的时候，注意要指定源地址，不指定的话，就用**出接口**作为源IP
若IBGP使用的不是物理接口或者物理接口和建立邻居关系的路由的出接口不一样，则要使用**更新源**(connected interface)指定一下源地址，否则无法建立邻居关系

```shell
# todo AR2：指定更新源
[R2-bgp]peer 4.4.4.4 connect-interface LoopBack 0
```

因为只在AR2配置了更新源，所以只能等待AR2发起TCP连接，意味着AR2拒绝AR4发送过来的TCP建立连接请求。在wireshark中能看到2.2.2.2和4.4.4.4建立TCP连接的报文

### **实验一$_3$：实现互通**

**目的**：建立AR4和AR6的BGP邻居关系，使用环回口地址建立BGP邻居关系
AR4使用环回地址`4.4.4.4`；AR6使用环回地址`6.6.6.6`

- **STEP4.0：** 配置环回地址

```shell
# todo AR4：配置环回地址
[R4]int lo 10
[R4-LoopBack10]ip add 44.44.44.44 32
[R4-LoopBack10]q
[R4]ip route-static 66.66.66.66 32 64.1.1.6

# todo AR6：配置环回地址
[R6]int lo 10
[R6-LoopBack10]ip add 66.66.66.66 32
[R6-LoopBack10]q
[R6]ip route-static 44.44.44.44 32 64.1.1.4
```

- **STEP4.1：** 测试连通

```shell
# todo AR4：测试连通
[R4]ping -a 44.44.44.44 66.66.66.66
```

能ping通。

- **STEP4.2：** BGP建立邻居关系

先写`as-number`，再写`connect-interface`，命令顺序不能反

这里老师的配置跟拓扑图不同，直接删除原来BGP的配置即可：

```shell
# todo AR6：删除原BGP配置
[R6]un bgp 24
Warning: All BGP configurations will be deleted. Continue? [Y/N]: y
```

然后配置建立BGP邻居关系：

```shell
# todo AR6：配置建立BGP邻居关系
[R6]bgp 6
[R6-bgp]peer 44.44.44.44 as-number 16

# todo AR4：配置建立BGP邻居关系
[R4]bgp 16
[R4-bgp]peer 66.66.66.66 as-number 6
```

- **STEP4.3：** 查表

```shell
# todo AR4：查看BGP邻居
[R4]dis bgp peer
 BGP local router ID : 34.1.1.4
 Local AS number : 16
 Total number of peers : 2                Peers in established state : 1
  Peer            V          AS  MsgRcvd  MsgSent  OutQ  Up/Down       State PrefRcv
  2.2.2.2         4          16       25       24     0 00:22:51 Established       1
  6.6.6.6         4           6        0        0     0 00:10:41        Idle       0

# todo AR4：查看BGP学习到的路由
[R4]dis bgp rou
 BGP Local router ID is 34.1.1.4 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 1
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
   i  1.1.1.1/32         12.1.1.1        0          100        0      1i

# todo AR4：查看路由表
[R4]dis ip rou
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 16       Routes : 16       
Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface
        2.2.2.2/32  OSPF    10   2           D   34.1.1.3        GigabitEthernet0/0/0
        3.3.3.3/32  OSPF    10   1           D   34.1.1.3        GigabitEthernet0/0/0
        4.4.4.4/32  Direct  0    0           D   127.0.0.1       LoopBack0
        ······
```

i：从BGP学习到的地址
但是路由表中没有该地址

所以此时有两个问题：
1.AR4和AR6邻居关系无法建立
2.AR4能通过AR2学习`1.1.1.1`路由条目，但是无法加表，因为AR4学习该地址及其下一跳，而AR2的`1.1.1.1`的下一跳是AR2的直连路由，但是对AR4来说是一个不可达地址，所以不加到AR4路由表中

- **STEP4.4：** 设置下一跳地址

在AR2上，把出AR2的地址设置为AR2本地地址，且保证该地址有效可达

```shell
# todo AR2：设置下一跳地址
[R2-bgp]peer 4.4.4.4 next-hop-local
```

- **STEP4.5：** 查看BGP更新(需要重置邻居关系)

```shell
# todo AR4：查看BGP学习的路由
<R4>dis bgp rou
 BGP Local router ID is 34.1.1.4 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 1
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
   i  1.1.1.1/32         12.1.1.1        0          100        0      1i
```

这里需要重置以下BGP邻居关系才能生效

```shell
# todo AR2：重置BGP
<R2>reset bgp all graceful 
```

如果在一台路由器配置，另一台路由器未及时生效，可以把BGP邻居关系重置一下。(graceful软重启，如果软重启不行就硬重启。)

- **STEP4.6：** 重启后查看

```shell
# todo AR4：查看BGP路由表
[R4-bgp]dis bgp rou
 BGP Local router ID is 34.1.1.4 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 1
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         2.2.2.2         0          100        0      1i

# todo AR4：查看BGP邻居关系
[R4-bgp]dis bgp pee
 BGP local router ID : 34.1.1.4
 Local AS number : 16
 Total number of peers : 2                Peers in established state : 1
  Peer            V          AS  MsgRcvd  MsgSent  OutQ  Up/Down       State PrefRcv
  2.2.2.2         4          16        4        3     0 00:01:49 Established       1
  66.66.66.66     4           6        0        0     0 00:07:37      Active       0
```

虽然路由表有了`*>i`，但是链路状态为`Active`。

- **STEP4.7：** 配置环回口为更新源

```shell
# todo AR4：配置环回口为更新源
[R4-bgp]peer 66.66.66.66 connect-interface  LoopBack 10
```

配置之后，AR4就会用`44.44.44.44`和对方`66.66.66.66`建立邻居关系。

- **STEP4.8：** 开始抓包

在AR4和AR6之间开始抓包，可以看到(`44.44.44.44`和`66.66.66.66`建立邻居关系)：

<img src="./174f70b475be45d992f58ad86b919d98.png">

EBGP建立邻居关系特性：
EBGP多跳，EBGP发送的所有报文TTL为1，即只能走一跳，如图所示：

<img src="./6fab1a3153754b5b91c8c4b62e290e56.png">

说白了EBGP默认只有直连才能建立EBGP邻居关系，中间不能有路由器，但是两个EBGP路由器中间有路由器的情况比较常见，所以可以把TTL设置大一点，而且两边都要设置。

- **STEP4.9：** 修改EBGP报文的TTL值

```shell
# todo AR6：修改EBGP报文的TTL值
[R6]bgp 6
[R6-bgp]peer 44.44.44.44 ebgp-max-hop 10

# todo AR4：修改EBGP报文的TTL值
[R4]bgp 16
[R4-bgp]peer 66.66.66.66 ebgp-max-hop 10
```

- **STEP4.99：** 查看

首先，查看抓包信息：

<img src="./d2a33e2aaac346faaf571e5dd2cd8726.png">

其次，查看BGP邻居关系

```shell
# todo AR6：查看BGP邻居关系
[R6-bgp]dis bgp pee
 BGP local router ID : 26.1.1.6
 Local AS number : 6
 Total number of peers : 1                Peers in established state : 1
  Peer            V          AS  MsgRcvd  MsgSent  OutQ  Up/Down       State PrefRcv
  44.44.44.44     4          16        3        2     0 00:00:06 Established       1
```

再次，查看路由表

```shell
# todo AR6：查看路由表
[R6-bgp]dis ip rou
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 11       Routes : 11       
Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface
        1.1.1.1/32  EBGP    255  0          RD   44.44.44.44     GigabitEthernet0/0/1
        6.6.6.6/32  Direct  0    0           D   127.0.0.1       LoopBack0
    44.44.44.44/32  Static  60   0          RD   64.1.1.4        GigabitEthernet0/0/1
    ······
```

此时已经有`1.1.1.1`这个路由条目。

- **STEP5.0：** AR6宣告网段

```shell
# todo AR6：宣告网段
[R6-bgp]network 6.6.6.6 32
```

- **STEP5.1：** 查看AR1路由表

```shell
# todo AR1：查看路由表
<R1>dis ip rou
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 8        Routes : 8        

Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface

        1.1.1.1/32  Direct  0    0           D   127.0.0.1       LoopBack0
       12.1.1.0/24  Direct  0    0           D   12.1.1.1        GigabitEthernet0/0/0
       12.1.1.1/32  Direct  0    0           D   127.0.0.1       GigabitEthernet0/0/0
     12.1.1.255/32  Direct  0    0           D   127.0.0.1       GigabitEthernet0/0/0
      127.0.0.0/8   Direct  0    0           D   127.0.0.1       InLoopBack0
      127.0.0.1/32  Direct  0    0           D   127.0.0.1       InLoopBack0
127.255.255.255/32  Direct  0    0           D   127.0.0.1       InLoopBack0
255.255.255.255/32  Direct  0    0           D   127.0.0.1       InLoopBack0

<R1>dis bgp rou
 BGP Local router ID is 12.1.1.1 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 1
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>   1.1.1.1/32         0.0.0.0         0                     0      i
```

AR1学习不到`6.6.6.6`。

- **STEP5.2：** 分析AR1学习不到`6.6.6.6`的原因

从AR6到AR1没问题，但是，因为(AR1->)AR2学习到的地址`6.6.6.6`的下一跳指向`66.66.66.66`，(AR2->)AR4的目的地址`6.6.6.6`的下一跳`66.66.66.66`单播不可达，所以加数据库，但是不加表，因此AR4需要使用更新源：

```shell
# todo AR4：查看BGP路由表
<R4>dis bgp rou
 BGP Local router ID is 34.1.1.4 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 2
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         2.2.2.2         0          100        0      1i
 *>   6.6.6.6/32         66.66.66.66     0                     0      6i

# todo AR4：更新源
[R4]bgp 16
[R4-bgp]peer 2.2.2.2 next-hop-local
```

- **STEP5.3：** 查看路由表

```shell
# todo AR1：查看路由表
<R1>dis ip rou
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 9        Routes : 9        
Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface
        1.1.1.1/32  Direct  0    0           D   127.0.0.1       LoopBack0
        6.6.6.6/32  EBGP    255  0           D   12.1.1.2        GigabitEthernet0/0/0
        ······
```

AR1路由表有`6.6.6.6`

- **STEP6.0：** 测试连通

```shell
# todo AR1：ping AR6
<R1>ping -a 1.1.1.1 6.6.6.6
  PING 6.6.6.6: 56  data bytes, press CTRL_C to break
    Request time out
    Request time out
    Request time out
    Request time out
    Request time out

  --- 6.6.6.6 ping statistics ---
    5 packet(s) transmitted
    0 packet(s) received
    100.00% packet loss
```

不通

- **STEP6.1：** 分析ping不同原因，配置MPLS

注：
1.AR3路由黑洞：转发到该接口上的数据包都会被丢掉
2.解决办法：重分发，静态路由，BGP，MPLS······
3.MPLS(多协议标签交换)，当有标签的时候，基于标签转发，不再基于路由表转发
4.id建议使用环回口地址，确保环回口地址单播可达，因为要通过LSR-ID建立TCP连接来分标签

```shell
# todo AR2：配置MPLS
[R2]mpls lsr-id 2.2.2.2  # 配LSR-ID
[R2]mpls  # 启用MPLS
Info: Mpls starting, please wait... OK!
[R2-mpls]mpls ldp  # 启用MPLS LDB

# todo 接口配置MPLS
[R2]int g0/0/1
[R2-GigabitEthernet0/0/1]mpls     
[R2-GigabitEthernet0/0/1]mpls ldp

# todo AR3：配置MPLS
[R3]mpls lsr-id 3.3.3.3
[R3]mpls
Info: Mpls starting, please wait... OK!
[R3-mpls]mpls ldp

# todo AR3：接口配置MPLS
[R3-mpls-ldp]int g0/0/0
[R3-GigabitEthernet0/0/0]mpls
[R3-GigabitEthernet0/0/0]mpls ldp
[R3-GigabitEthernet0/0/0]int g0/0/1
[R3-GigabitEthernet0/0/1]mpls
[R3-GigabitEthernet0/0/1]mpls ldp

# todo AR4：配置MPLS
[R4]mpls lsr-id 4.4.4.4
[R4]mpls
Info: Mpls starting, please wait... OK!
[R4-mpls]mpls ldp
[R4-mpls-ldp]int g0/0/0
[R4-GigabitEthernet0/0/0]mpls
[R4-GigabitEthernet0/0/0]mpls ldp 
```

- **STEP6.2：** 查看MPLS转发表

```shell
# todo AR2：查看MPLS转发表
[R2-GigabitEthernet0/0/1]dis mpls lsp
-------------------------------------------------------------------------------
                 LSP Information: LDP LSP
-------------------------------------------------------------------------------
FEC                In/Out Label  In/Out IF                      Vrf Name       
3.3.3.3/32         NULL/3        -/GE0/0/1                                     
3.3.3.3/32         1024/3        -/GE0/0/1                                     
2.2.2.2/32         3/NULL        -/-                                           
4.4.4.4/32         NULL/1025     -/GE0/0/1                                     
4.4.4.4/32         1025/1025     -/GE0/0/1   

# todo AR3：查看MPLS转发表
[R3-GigabitEthernet0/0/1]dis mpls lsp
-------------------------------------------------------------------------------
                 LSP Information: LDP LSP
-------------------------------------------------------------------------------
FEC                In/Out Label  In/Out IF                      Vrf Name       
3.3.3.3/32         3/NULL        -/-                                           
2.2.2.2/32         NULL/3        -/GE0/0/0                                     
2.2.2.2/32         1024/3        -/GE0/0/0                                     
4.4.4.4/32         NULL/3        -/GE0/0/1                                     
4.4.4.4/32         1025/3        -/GE0/0/1                                     

# todo AR4：查看MPLS转发表
[R4-GigabitEthernet0/0/0]dis mpls lsp
-------------------------------------------------------------------------------
                 LSP Information: LDP LSP
-------------------------------------------------------------------------------
FEC                In/Out Label  In/Out IF                      Vrf Name       
2.2.2.2/32         NULL/1024     -/GE0/0/0                                     
2.2.2.2/32         1024/1024     -/GE0/0/0                                     
3.3.3.3/32         NULL/3        -/GE0/0/0                                     
3.3.3.3/32         1025/3        -/GE0/0/0                                     
44.44.44.44/32     3/NULL        -/-                                           
4.4.4.4/32         3/NULL        -/-                                           
```

In进来撕掉标签
Out出去添加标签

- **STEP6.3：** 测试连通

```shell
<R1>ping -a 1.1.1.1 6.6.6.6
  PING 6.6.6.6: 56  data bytes, press CTRL_C to break
    Request time out
    Request time out
    Request time out
    Request time out
    Request time out

  --- 6.6.6.6 ping statistics ---
    5 packet(s) transmitted
    0 packet(s) received
    100.00% packet loss
```

还是不通，因为没有关闭递归查找：

- **STEP6.4：** 关闭递归查找

```shell
# todo 关闭递归查找
[R2]route recursive-lookup tunnel 
[R3]route recursive-lookup tunnel 
[R4]route recursive-lookup tunnel 
```

但凡少配一个路由器，下面都不能通

- **STEP6.5：** 测试连通

```shell
<R1>ping -a 1.1.1.1 6.6.6.6
  PING 6.6.6.6: 56  data bytes, press CTRL_C to break
    Reply from 6.6.6.6: bytes=56 Sequence=1 ttl=252 time=70 ms
    Reply from 6.6.6.6: bytes=56 Sequence=2 ttl=252 time=40 ms
    Reply from 6.6.6.6: bytes=56 Sequence=3 ttl=252 time=50 ms
    Reply from 6.6.6.6: bytes=56 Sequence=4 ttl=252 time=40 ms
    Reply from 6.6.6.6: bytes=56 Sequence=5 ttl=252 time=30 ms

  --- 6.6.6.6 ping statistics ---
    5 packet(s) transmitted
    5 packet(s) received
    0.00% packet loss
    round-trip min/avg/max = 30/46/70 ms
```

[次末跳(倒数第二跳)](https://blog.csdn.net/weixin_45404784/article/details/105387520)：边界LSR将本地的直连网段传递给MPLS域内邻居后，LDP给该网段分配标签号为3，告知倒数第二跳设备它的身份；导致倒数第二跳设备在查询LFIB表后，已知转发路径的前提下提前弹出标签，使得最后一跳路由器均只需要查询FIB表；否则最后一跳路由器在查询LFIB表后，弹出标签还需要查询FIB。

## BGP路径选择属性

<img src="./435538e229b646a8873d63c04964a4f2.png">

<img src="./9f20a45b0a4f4e80b137c23021639d77.png">

<img src="./91a63ab27c944bc7b32b2705550ccca2.png">

<img src="./6ff870a46725443e96b78ea53fd071bc.png">

[【每日干货】华为13条BGP选路原则详解](https://www.bilibili.com/read/cv12135573)

### **原理：BGP选路原则**

13条BGP选路原则(中的几条)：

- 2、Prefer highest local preference (global within AS).：本地优先级最高的路由<**本地优先级用于AS内部**>
- 3、Prefer route originated by the local router (next hop = `0.0.0.0`).：当前路由器**通告**的路由
- 4、Prefer shortest AS path.：**AS路径最短的路由**
- 7、Prefer EBGP path over IBGP path.：**外部路径(EBGP)优先于内部路径(IBGP)**
- 8、Prefer the path through the closest IGP neighbor：选择经过最近的IGP邻居的路径，即**IBGP下一跳首选AS中最短的内部路径(OSPF、RIP开销cost值最小的)作为下一跳**
- 9、balance traffic：如果配置了maximum-path(**默认为1**), 会安装多条路由到路由表<但是BGP继续比，只有一个打上>
- 10、Prefer oldest route for EBGP paths.：都是外部路径，选**最先**收到的（最旧的）<目的是减少抖动>
- 11、Prefer the path with the lowest neighbor BGP router ID.：首选来自**最低路由ID**的BGP路由器的路由

### **实验二$_1$：BGP属性:协议首选值PrefVal(preference value)**

PrefVal是华为特有的属性

- **方法一**：直接在BGP修改

- **STEP1.0：** 网络拓扑

直接打开老师的拓扑

<img src="./68c11ce8b9a04b78a99a55235e3f6a01.png">

注意：AR1有环回地址`1.1.1.1`，AR4有环回地址`4.4.4.4`。

- **STEP1.1：** 改名(略)

- **STEP2.0：** 查看

```shell
# todo AR2：查看BGP邻居关系
[R2]dis bgp pee
 BGP local router ID : 12.1.1.2
 Local AS number : 1
 Total number of peers : 3		  Peers in established state : 3
  Peer            V          AS  MsgRcvd  MsgSent  OutQ  Up/Down       State Pre fRcv
  12.1.1.1        4           1       22       22     0 00:18:46 Established       2
  23.1.1.3        4          24       21       23     0 00:18:46 Established       1
  26.1.1.6        4          24       21       23     0 00:18:46 Established       1

# todo AR2：查看BGP路由表
[R2]dis bgp rou
 BGP Local router ID is 12.1.1.2 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 4
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         12.1.1.1        0          100        0      i
 *>   4.4.4.4/32         23.1.1.3                              0      24i
 *                       26.1.1.6                              0      24i
   i  12.1.1.0/24        12.1.1.1        0          100        0      i
```

- **STEP2.1：** 修改优先级

```shell
# todo AR2：修改优先级
[R2]bgp 1
[R2-bgp]peer 26.1.1.6 preferred-value 1

# todo AR2：查看BGP路由表
[R2-bgp]dis bgp rou
 BGP Local router ID is 12.1.1.2 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 4
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         12.1.1.1        0          100        0      i
 *>   4.4.4.4/32         26.1.1.6                              1      24i
 *                       23.1.1.3                              0      24i
   i  12.1.1.0/24        12.1.1.1        0          100        0      i
```

看到AR2到`4.4.4.4`的第一个下一跳由`23.1.1.3`改为`26.1.1.6`，并且优先级变为1。

- **STEP2.2：** 实验现象看完后，删除配置，利于做下一个实验

```shell
# todo AR2：删除配置
[R2-bgp]un peer 26.1.1.6 preferred-value

# todo AR2：查看BGP路由表
[R2-bgp]dis bgp rou
 BGP Local router ID is 12.1.1.2 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 4
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         12.1.1.1        0          100        0      i
 *>   4.4.4.4/32         23.1.1.3                              0      24i
 *                       26.1.1.6                              0      24i
   i  12.1.1.0/24        12.1.1.1        0          100        0      i
```

- **方法二**：通过配置前缀列表和ACL进行修改

- **STEP3.0：** 使用前缀列表

```shell
# todo AR2：配置前缀列表
[R2]ip ip-prefix dcm permit 4.4.4.4 32

# todo AR2：配置路由策略
[R2]route-policy ?
  STRING<1-40>  Name of the route policy
  1             Name of the route policy
  ben           Name of the route policy
[R2]route-policy PV permit node ?
  INTEGER<0-65535>  Index of the node
[R2]route-policy PV permit node 10 
Info: New Sequence of this List.

# todo AR2：匹配前缀列表
[R2-route-policy]if-match ip-prefix ?
  STRING<1-169>  Specify prefix-list name
[R2-route-policy]if-match ip-prefix dcm

# todo AR2：修改优先级
[R2-route-policy]apply preferred-value ?
  INTEGER<0-65535>  Specify BGP Preferred-value(weight) value for routing table
[R2-route-policy]apply preferred-value 10

# todo AR2：配置路由策略
[R2]route-policy PV permit node 20
Info: New Sequence of this List.

# todo AR2：在BGP应用前缀列表
[R2]bgp 1
[R2-bgp]peer 26.1.1.6 route-policy PV import 

# todo AR2：查看BGP路由表
[R2-bgp]dis bgp rou
 BGP Local router ID is 12.1.1.2 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 4
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         12.1.1.1        0          100        0      i
 *>   4.4.4.4/32         26.1.1.6                              10     24i
 *                       23.1.1.3                              0      24i
   i  12.1.1.0/24        12.1.1.1        0          100        0      i
```

prefVal是**私有属性**，不能转发，且这个属性只能**接收方向**修改

- **STEP3.1：** 删除配置，利于做下一个实验

```shell
# todo AR2：删除配置
[R2-bgp]un peer 26.1.1.6 route-policy PV import
```

### **实验二$_2$：BGP属性:本地优先级最高的路由LocPrf(Local Preference)**

LocPrf只能在AS内部的IBGP传递时使用，就是说EBGP之间不能使用
可以用在IBGP的In和Out方向，只能用在EBGP的In方向(虽然不能传递，但是可以在本地修改)

**目的**：AR2选择AR6的路由路径作为最优

- **STEP4.0：** 修改LocPrf

```shell
# todo AR2：路由策略
[R2]route-policy LP permit node 10 
Info: New Sequence of this List.

# todo AR2：前缀列表
[R2-route-policy]if-match ip-prefix dcm  # 之前配置的前缀列表dcm
[R2-route-policy]apply local-preference 110  # 修改locprf

# todo AR2：查看BGP路由表
[R2]dis bgp rou
 BGP Local router ID is 12.1.1.2 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 4
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         12.1.1.1        0          100        0      i
 *>   4.4.4.4/32         23.1.1.3                              0      24i
 *                       26.1.1.6                              0      24i
   i  12.1.1.0/24        12.1.1.1        0          100        0      i

# todo AR2：BGP启用路由策略
[R2]bgp 1
[R2-bgp]peer 26.1.1.6 route-policy LP import 

# todo AR2：查看BGP路由表
[R2-bgp]dis bgp rou
 BGP Local router ID is 12.1.1.2 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 4
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         12.1.1.1        0          100        0      i
 *>   4.4.4.4/32         26.1.1.6                   110        0      24i
 *                       23.1.1.3                              0      24i
   i  12.1.1.0/24        12.1.1.1        0          100        0      i
```

配置路由策略前后，AR2到`4.4.4.4`的下一跳地址优先级改变。

- **STEP4.1：** 删除配置

```shell
# todo AR2：删除配置
[R2-bgp]un peer 26.1.1.6 route-policy LP import
```

- **STEP4.2：** 做一个无效配置，给EBGP修改Out方向的LocPrf值

选AR6(一个EBGP)作为配置对象

```shell
# todo AR2：查看路由表
[R2-bgp]dis bgp rou
 BGP Local router ID is 12.1.1.2 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 4
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         12.1.1.1        0          100        0      i
 *>   4.4.4.4/32         23.1.1.3                              0      24i
 *                       26.1.1.6                              0      24i
   i  12.1.1.0/24        12.1.1.1        0          100        0      i

# todo AR6：配置前缀列表
[R6]ip ip-prefix dcm4 permit 4.4.4.4 32

# todo AR6：配置路由策略
[R6]route-policy LP permit node 10
Info: New Sequence of this List.
[R6-route-policy]if-match ip-prefix dcm4  # 应用前缀列表
[R6-route-policy]apply local-preference 120  # 修改LocPrf

# todo AR6：将路由策略应用到发送给AR2的反向
[R6]bgp 24
[R6-bgp]peer 26.1.1.2 route-policy LP export  # 注意是出方向

# todo AR2：重置邻居关系，一般配置后BGP关系需要重置才能建立
<R2>reset bgp all graceful

# todo AR2：邻居关系建立后，再查看BGP路由表
<R2>dis bgp rou
 BGP Local router ID is 12.1.1.2 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 4
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         12.1.1.1        0          100        0      i
 *>   4.4.4.4/32         23.1.1.3                              0      24i
 *                       26.1.1.6                              0      24i
   i  12.1.1.0/24        12.1.1.1        0          100        0      i
```

可以看到AR2路由表的下一跳地址的LocPrf不变，证明：EBGP不能在出方向修改LocPrf(只能在入方向修改LocPrf)。

- **STEP4.3：** 删除配置，便于做下一个实验(其实这个配置删不删无所谓，本来配置就无法生效)

```shell
# todo AR6：删除配置
[R6-bgp]un peer 26.1.1.2 route-policy LP export
```

- **STEP4.4：** 配置入方向

**目的**：在AR4上，选择AR6作为最优路径

```shell
# todo AR6：配置前缀列表
[R6]ip ip-prefix dcm1 permit 1.1.1.1 32

# todo AR6：配置路由策略
[R6]route-policy LP permit node 10
Info: You are overwriting this sequence.

# todo AR6：应用前缀列表
[R6-route-policy]if-match ip-prefix dcm1 

# todo AR6：BGP应用路由策略
[R6]bgp 24
[R6-bgp]peer 64.1.1.4 route-policy LP export  # 注意是出方向

# todo AR4：重置BGP邻居关系
<R4>reset bgp all graceful

# todo AR4：查看BGP邻居关系
<R4>dis bgp peer
 BGP local router ID : 34.1.1.4
 Local AS number : 24
 Total number of peers : 2		  Peers in established state : 2
  Peer            V          AS  MsgRcvd  MsgSent  OutQ  Up/Down       State Pre fRcv
  34.1.1.3        4          24        3        4     0 00:00:11 Established       1
  64.1.1.6        4          24        3        3     0 00:00:11 Established       1

# todo AR4：查看BGP路由表
<R4>dis bgp rou
 BGP Local router ID is 34.1.1.4 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 3
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         64.1.1.6                   120        0      1i
 * i                     34.1.1.3                   100        0      1i
 *>   4.4.4.4/32         0.0.0.0         0                     0      i
```

可以看到AR4到`64.1.1.6`的下一跳的`LocPrf`变为120

- **STEP4.5：** 删除配置

```shell
# todo AR6：删除配置
[R6-bgp]un peer 64.1.1.4 route-policy LP export
```

### **实验二$_3$：BGP属性:经过的所有AS号矢量AS-Path**

AS-Path用于防环，经过的AS越少，那么路由越优先

AS-Path只能在EBGP间使用(In和Out可以使用)，不能在IBGP间使用。

AS-Path只能加，不能减。这意味着，如果想让一个路径最优，不能减少这个路径的Path值，只能让另一条路径的Path值增加。

- **STEP5.0：** 修改AS-Path

```shell
# todo AR2：查看BGP路由表
<R2>dis bgp rou
 BGP Local router ID is 12.1.1.2 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 4
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         12.1.1.1        0          100        0      i
 *>   4.4.4.4/32         23.1.1.3                              0      24i
 *                       26.1.1.6                              0      24i
   i  12.1.1.0/24        12.1.1.1        0          100        0      i

# todo AR3：配置前缀列表
[R3]ip ip-prefix dcm4 permit 4.4.4.4 32

# todo AR3：配置路由策略
[R3]route-policy AS permit node 10
Info: New Sequence of this List.
[R3-route-policy]if-match ip-prefix dcm4  # 应用前缀列表
[R3-route-policy]apply as-path ?
  INTEGER<1-4294967295>  AS number in asplain format (number<1-4294967295>)
  STRING<3-11>           AS number in asdot format                              
                         (number<1-65535>.number<0-65535>)
  none                   Clear as-path list
[R3-route-policy]apply as-path 24 24 ?
  INTEGER<1-4294967295>  AS number in asplain format (number<1-4294967295>)
  STRING<3-11>           AS number in asdot format                              
                         (number<1-65535>.number<0-65535>)
  additive               Append to original As Number
  overwrite              Overwrite original As Number	
[R3-route-policy]apply as-path 24 24 additive ?
  <cr>  Please press ENTER to execute command 
[R3-route-policy]apply as-path 24 24 additive  # 修改as-path

# todo AR3：BGP应用路由策略
[R3]bgp 24
[R3-bgp]peer 23.1.1.2 route-policy AS export 

# todo AR2：重启BGP邻居关系
<R2>reset bgp all graceful 

# todo AR2：查看BGP路由表
<R2>dis bgp rou
 BGP Local router ID is 12.1.1.2 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 4
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         12.1.1.1        0          100        0      i
 *>   4.4.4.4/32         26.1.1.6                              0      24i
 *                       23.1.1.3                              0      24 24 24i
   i  12.1.1.0/24        12.1.1.1        0          100        0      i
```

注：
1.additive：在原来AS-Path上增加几个AS-Path，这样既防环，又不会因为水平分割而无法进入某些AS中
2.修改AS-Path前，只有一个24
3.修改AS-Path后，一共3个24，如果用override就是覆盖，就只有2个24

- **STEP5.1：** 删除配置

```shell
# todo AR3：删除配置
[R3-bgp]un peer 23.1.1.2 route-policy AS export
```

### **实验二$_4$：BGP属性:起源属性Origin**

起源属性Origin全局范围有效
优先级由高到低：i（IBGP） > e（EBGP） > ?（重分发）

- **STEP6.0：** 修改起源属性Origin

```shell
# todo AR3：
[R3]route-policy OR permit node 10
Info: New Sequence of this List.

[R3-route-policy]if-match ip-prefix dcm4

[R3-route-policy]apply origin ?
  egp         Remote EGP
  igp         Local IGP
  incomplete  Unknown heritage
[R3-route-policy]apply origin incomplete

# todo AR3：
[R3]bgp 24
[R3-bgp]peer 23.1.1.2 route-policy OR export 

# todo AR2：查看BGP路由表
<R2>dis bgp rou
 BGP Local router ID is 12.1.1.2 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 4
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         12.1.1.1        0          100        0      i
 *>   4.4.4.4/32         23.1.1.3                              0      24i
 *                       26.1.1.6                              0      24i
   i  12.1.1.0/24        12.1.1.1        0          100        0      i

# todo AR2：重置BGP邻居关系
<R2>reset bgp all

# todo AR2：查看BGP路由表
<R2>dis bgp rou
 BGP Local router ID is 12.1.1.2 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 4
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         12.1.1.1        0          100        0      i
 *>   4.4.4.4/32         26.1.1.6                              0      24i
 *                       23.1.1.3                              0      24?
   i  12.1.1.0/24        12.1.1.1        0          100        0      i
```

- **STEP6.1：** 删除配置

```shell
# todo AR3：删除配置
[R3-bgp]un peer 23.1.1.2 route-policy OR export 
```

STEAM上的编程游戏：Linux】Hacknet、while True: learn()、汇编】SHENZHEN I/O

### **实验二$_5$：BGP属性:代价值MED**

重分发进来的路由条目的度量值，在**AS内**有效，即EBGP之间

- **STEP7：** 配置MED(静态路由&路由引入)

```shell
# todo AR1：静态路由
[R1]ip route-static 11.11.11.11 32 NULL 0

# todo AR1：设置路由引入的MED
[R1]bgp 1
[R1-bgp]import-route static ?  
  med           Med for imported route
  route-policy  Specify a route policy
  <cr>          Please press ENTER to execute command 
[R1-bgp]import-route static med 666

# todo AR2：查看EBGP的BGP路由表
[R2]dis bgp rou
 BGP Local router ID is 12.1.1.2 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 5
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         12.1.1.1        0          100        0      i
 *>   4.4.4.4/32         23.1.1.3                              0      24i
 *                       26.1.1.6                              0      24i
 *>i  11.11.11.11/32     12.1.1.1        666        100        0      ?
   i  12.1.1.0/24        12.1.1.1        0          100        0      i

# todo AR4：查看IBGP的BGP路由表
<R4>dis bgp rou
 BGP Local router ID is 34.1.1.4 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 5
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         34.1.1.3                   100        0      1i
 * i                     64.1.1.6                   100        0      1i
 *>   4.4.4.4/32         0.0.0.0         0                     0      i
 *>i  11.11.11.11/32     34.1.1.3                   100        0      1?
 * i                     64.1.1.6                   100        0      1?
```

## **实验三：BGP链路聚合**

BGP聚合方式：自动聚合、手动聚合
BGP只能对**重分发**进来的路由条目进行聚合，且全部按**主类网络掩码**进行聚合

- **STEP8.0：** 配置链路聚合(自动聚合)

```shell
# todo AR1：配置链路自动聚合
[R1-bgp]summary automatic 
Info: Automatic summarization is valid only for the routes imported through the import-route command.

# todo AR2：查看BGP路由表
[R2]dis bgp rou
 BGP Local router ID is 12.1.1.2 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 5
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         12.1.1.1        0          100        0      i
 *>   4.4.4.4/32         23.1.1.3                              0      24i
 *                       26.1.1.6                              0      24i
 *>i  11.0.0.0           12.1.1.1                   100        0      ?
   i  12.1.1.0/24        12.1.1.1        0          100        0      i
```

- **STEP8.1：** 配置链路聚合(手动聚合)

先添加聚合需要使用的环回地址：

```shell
# todo AR1：配置环回地址
[R1-bgp]int lo 5
[R1-LoopBack5]ip add 10.0.0.1 24
[R1-LoopBack5]ip add 10.0.1.1 24 sub
[R1-LoopBack5]ip add 10.0.2.1 24 sub
[R1-LoopBack5]ip add 10.0.3.1 24 sub

# todo AR1：BGP宣告网段
[R1]bgp 1
[R1-bgp]network 10.0.0.1 24
[R1-bgp]network 10.0.1.1 24
[R1-bgp]network 10.0.2.1 24
[R1-bgp]network 10.0.3.1 24

# todo AR2：查看BGP路由表
[R2]dis bgp rou
 BGP Local router ID is 12.1.1.2 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 9
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         12.1.1.1        0          100        0      i
 *>   4.4.4.4/32         23.1.1.3                              0      24i
 *                       26.1.1.6                              0      24i
 *>i  10.0.0.0/24        12.1.1.1        0          100        0      i
 *>i  10.0.1.0/24        12.1.1.1        0          100        0      i
 *>i  10.0.2.0/24        12.1.1.1        0          100        0      i
 *>i  10.0.3.0/24        12.1.1.1        0          100        0      i
 *>i  11.0.0.0           12.1.1.1                   100        0      ?
   i  12.1.1.0/24        12.1.1.1        0          100        0      i
```

此时AR2学到的是明细路由，这种情况该使用手动聚合：

```shell
# todo AR1：配置手动聚合
[R1-bgp]aggregate 10.0.0.0 22 detail-suppressed

# todo AR2：查看BGP路由表
[R2]dis bgp rou
 BGP Local router ID is 12.1.1.2 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 9
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         12.1.1.1        0          100        0      i
 *>   4.4.4.4/32         23.1.1.3                              0      24i
 *                       26.1.1.6                              0      24i
 *>i  10.0.0.0/22        12.1.1.1        0          100        0      i
 *>i  11.0.0.0           12.1.1.1                   100        0      ?
   i  12.1.1.0/24        12.1.1.1        0          100        0      i
```

detail-suppressed：详细抑制，只有汇总，不要明细
如果不加，仍然有明细路由，而且多了一个汇总

`undo sync`
同步是用来防止路由黑洞的
同步：如果BGP打开了同步，意思是说
BGP与IGP的同步功能。

缺省情况下，同步功能是关闭的。

## BGP路由反射器与联盟

### **实验四$_1$：路由反射器**

RR反射器防环：解决BGP水平分割问题(IBGP和EBGP的水平分割)
IBGP水平分割：通过IBGP学习的条目不能再发送给其他IBGP
EBGP水平分割：从某个接口上学习来的路由信息将不再从该接口上通告出去。就是从那个口进来的不再从这个口出去。

路由发射器RR：
1.当RR从客户机收到一个路由更新时，会将它发送给客户机和非客户机。
2.当RR从非客户机收到一个路由更新时，只发送给客户机路由器，而不发送给其他非客户机路由器
3.当RR从EBGP收到一个路由更新时，会将它发送给客户机和非客户机。

- **STEP1.0：** 网络拓扑

直接打开老师拓扑：

<img src="./5d9cd2cec6bc4b1d8f2dbc0185c9cb1f.png">

设备角色：R3服务器，其余客户机

- **STEP1.1：** 改名(略)

- **STEP2：** 配置路由发射器

**目的**：AR4从IBGP学习到1.1.1.1条目

```shell
# todo AR4：查看BGP路由表
[R4]dis bgp rou  # 目前没有BGP路由条目

# todo AR3：设置RR和Client
[R3]bgp 2     
[R3-bgp]peer 23.1.1.2 reflect-client  # 设置为RR，23.1.1.2为client

# todo AR4：查看BGP路由表
[R4]dis bgp rou
 BGP Local router ID is 34.1.1.4 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 1
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         12.1.1.1        0          100        0      1i

# todo AR3：设置RR和Client
[R3-bgp]peer 34.1.1.4 reflect-client  # 设置为RR，34.1.1.4为client
```

**目的**：AR5从IBGP学习到`1.1.1.1`条目

```shell
# todo AR5：查看BGP路由表
[R5]dis bgp rou  # 目前没有BGP路由条目

# todo AR4：设置RR和Client
[R4]bgp 2
[R4-bgp]peer 45.1.1.5 reflect-client  # 设置为RR，45.1.1.5为client

# todo AR5：查看BGP路由表
[R5]dis bgp rou
 BGP Local router ID is 45.1.1.5 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 1
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         12.1.1.1        0          100        0      1i

# todo AR5：BGP详细信息
[R5]dis bgp rou 1.1.1.1

 BGP local router ID : 45.1.1.5
 Local AS number : 2
 Paths:   1 available, 1 best, 1 select
 BGP routing table entry information of 1.1.1.1/32:
 From: 45.1.1.4 (34.1.1.4)
 Route Duration: 00h01m00s  
 Relay IP Nexthop: 45.1.1.4
 Relay IP Out-Interface: GigabitEthernet0/0/0
 Original nexthop: 12.1.1.1
 Qos information : 0x0
 AS-path 1, origin igp, MED 0, localpref 100, pref-val 0, valid, internal, best, select, active, pre 255, IGP cost 4
 Originator:  12.1.1.2
 Cluster list: 34.1.1.4, 23.1.1.3
 Not advertised to any peer yet
```

### **实验四$_2$：联邦**

大AS中建立小AS，AS外部建立邻居关系通过大AS，

- AS1：
  - AR1
- AS2：
  - 64512：AR2、AR3
  - 64513：AR4、AR5

- **STEP3.0：** 删除旧的BGP配置

```shell
# todo AR2：删除旧的BGP配置
[R2]un bgp 2
Warning: All BGP configurations will be deleted. Continue? [Y/N]: y

# todo AR3：删除旧的BGP配置
[R3]un bgp 2
Warning: All BGP configurations will be deleted. Continue? [Y/N]: y

# todo AR4：删除旧的BGP配置
[R4]undo bgp 2
Warning: All BGP configurations will be deleted. Continue? [Y/N]: y

# todo AR5：删除旧的BGP配置
[R5]undo bgp 2
Warning: All BGP configurations will be deleted. Continue? [Y/N]: y
```

- **STEP3.1：** 配置联邦

**目的**：让AR2和AR3建立IBGP邻居关系，AR2和AR1建立EBGP邻居关系，AR3和AR4建立EBGP邻居关系

```shell
# todo AR2：配置联邦
[R2]bgp 64512
[R2-bgp]confederation id 2  # 声明大AS号
[R2-bgp]peer 23.1.1.3 as-number 64512  # 建立IBGP关系
[R2-bgp]peer 12.1.1.1 as-number 1  # 建立EBGP关系

# todo AR3：配置联邦
[R3]bgp 64512
[R3-bgp]confederation id 2  # 声明大AS号
[R3-bgp]peer 23.1.1.2 as-number 64512  # 建立EBGP关系
[R3-bgp]peer 34.1.1.4 as-number 64513  # 建立IBGP关系

# todo AR2：查看BGP邻居关系
[R2-bgp]dis bgp peer
 BGP local router ID : 12.1.1.2
 Local AS number : 16
 Total number of peers : 1                Peers in established state : 1
  Peer            V          AS  MsgRcvd  MsgSent  OutQ  Up/Down       State PrefRcv
  12.1.1.1        4           1        3        2      0 00:00:50 Established       1
  23.1.1.3        4       64512        6        8      0 00:24:20 Established       0

# todo AR1：查看BGP邻居关系
[R1]dis bgp peer
 BGP local router ID : 12.1.1.1
 Local AS number : 1
 Total number of peers : 1                Peers in established state : 0
  Peer            V          AS  MsgRcvd  MsgSent  OutQ  Up/Down       State PrefRcv
  12.1.1.2        4           2        0        0     0 00:06:36      Active       0
```

此时，AR1的邻居`12.1.1.1`的AS号为2，没问题。
但是AR3的AS号为64513，对AR4来说，AR4不知道AR3的AS号64513是大AS还是小AS，就用AS2建立邻居关系，邻居关系就建立不起来，因此需要使用以下命令，让AS号为64512和AS号为64513的路由器建立邻居关系

- **STEP3.2：** 配置联邦

**目的**：让AR3和AR4建立IBGP邻居关系，AR4和AR5建立EBGP邻居关系，都和AR1建立IBGP邻居关系

```shell
# todo AR3：配置联邦
[R3-bgp]un peer 34.1.1.4 as-number 64513  # 不能先指邻居，应该先confederation peer-as再peer
[R3-bgp]confederation peer-as 64513  # 指定属于同一个联盟的各子自治系统号，即让(自己)64512和(对方)64513建立邻居关系使用小AS号
[R3-bgp]peer 34.1.1.4 as-number 64513  # 建立EBGP关系

# todo AR4：配置联邦
[R4]bgp 64513
[R4-bgp]confederation id 2  # 声明大AS号
[R4-bgp]confederation peer-as 64512
[R4-bgp]peer 34.1.1.3 as-number 64512  # 建立IBGP关系
[R4-bgp]peer 45.1.1.5 as-number 64513  # 建立EBGP关系

# todo AR5：配置联邦
[R5]bgp 64513
[R5-bgp]confederation id 2  # 声明大AS号
[R5-bgp]peer 45.1.1.4 as-number 64513  # 建立IBGP关系

# todo AR5：查看BGP路由表
[R5-bgp]dis bgp rou
 BGP Local router ID is 45.1.1.5 
 Status codes: * - valid, > - best, d - damped,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 Total Number of Routes: 1
      Network            NextHop        MED        LocPrf    PrefVal Path/Ogn
 *>i  1.1.1.1/32         12.1.1.1        0          100        0      (64512) 1i
```
