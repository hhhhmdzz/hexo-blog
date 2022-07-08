---
title: ICT战队-第6次笔记
subtitle: 
summary: OSPF协议 RIP协议
description: OSPF协议 RIP协议
keyworks: 
author: hhhhmdzz
language: zh-Hans
timezone: Asia/Shanghai
date: 2022-06-29 10:50:06
categories: 
- ICT战队数据通信
tags: 
- HCIA
- 华为eNSP
mathjax: true
---

# ICT战队-第6次笔记

[toc]

## OSPF

### **原理：OSPF**

- **①OSPF网络类型**

网络类型：点到点、广播、非广播多路访问、点到多点
1.点到点、点到多点网络类型不选DR和BDR；广播、非广播网络类型才选DR和BDR，且使用224.0.0.5、224.0.0.6这两个地址
2.DR是**端口**的角色，不是路由器的角色
3.华为默认Serial串行口默认封装PPP
4.非广播多路访问：帧中继：不能发广播帧，但是又是一个广播型网络，所以把广播帧封装成单播帧
5.OSPF正常使用224.0.0.5发送OSPF数据包，DROther发送**更新**给DR和BDR使用224.0.0.6

```shell
# todo 修改OSPF路由协议
[R3]int s2/0/0
[R3-Serial2/0/0]ospf network-type ?
  broadcast  Specify OSPF broadcast network
  nbma       Specify OSPF NBMA network
  p2mp       Specify OSPF point-to-multipoint network
  p2p        Specify OSPF point-to-point network
[R3-Serial2/0/0]ospf network-type 
```

### **实验一：虚链路**

虚链路：解决非骨干区域连接非骨干区域问题。
做Virtual Link只找两个区域的ABR(区域边界路由器)。
找俩个区域的ABR，让他们互指Virtual-Link即可。

- **STEP1：** 网络拓扑

<img src="./60a44e765e8940cfa3e33b8f01d5cb50.png">

- **STEP2：** 配置虚链路

```shell
# todo 查看OSPF区域的邻居关系
[R]dis ospf peer
```

```shell
# todo AR2：指向对方RID
[R2]ospf
[R2-ospf-1]
[R2-ospf-1]dis ospf peer

   OSPF Process 1 with Router ID 10.0.2.2
     Neighbors 

 Area 0.0.0.0 interface 12.1.1.2(GigabitEthernet0/0/0)'s neighbors
 Router ID: 10.0.1.1         Address: 12.1.1.1        
   State: Full  Mode:Nbr is  Slave  Priority: 1
   DR: 12.1.1.2  BDR: 12.1.1.1  MTU: 0    
   Dead timer due in 40  sec 
   Retrans timer interval: 5 
   Neighbor is up for 00:06:24     
   Authentication Sequence: [ 0 ] 

     Neighbors 

 Area 0.0.0.1 interface 23.1.1.2(GigabitEthernet0/0/1)'s neighbors
 Router ID: 10.0.3.3         Address: 23.1.1.3        
   State: Full  Mode:Nbr is  Master  Priority: 1
   DR: 23.1.1.3  BDR: 23.1.1.2  MTU: 0    
   Dead timer due in 30  sec 
   Retrans timer interval: 5 
   Neighbor is up for 00:06:19     
   Authentication Sequence: [ 0 ] 

[R2-ospf-1]a 1
[R2-ospf-1-area-0.0.0.1]vlink-peer 10.0.3.3  # 对方的RID

# todo AR3：指向对方RID
[R3]ospf
[R3-ospf-1]a 1
[R3-ospf-1-area-0.0.0.1]dis ospf peer

   OSPF Process 1 with Router ID 10.0.3.3
     Neighbors 

 Area 0.0.0.1 interface 23.1.1.3(GigabitEthernet0/0/0)'s neighbors
 Router ID: 10.0.2.2         Address: 23.1.1.2        
   State: Full  Mode:Nbr is  Slave  Priority: 1
   DR: 23.1.1.3  BDR: 23.1.1.2  MTU: 0    
   Dead timer due in 31  sec 
   Retrans timer interval: 5 
   Neighbor is up for 00:07:30     
   Authentication Sequence: [ 0 ] 

     Neighbors 

 Area 0.0.0.2 interface 34.1.1.3(GigabitEthernet0/0/1)'s neighbors
 Router ID: 34.1.1.4         Address: 34.1.1.4        
   State: Full  Mode:Nbr is  Master  Priority: 1
   DR: 34.1.1.3  BDR: 34.1.1.4  MTU: 0    
   Dead timer due in 39  sec 
   Retrans timer interval: 5 
   Neighbor is up for 00:04:02     
   Authentication Sequence: [ 0 ] 

[R3-ospf-1-area-0.0.0.1]vlink-peer 10.0.2.2  # 对方的RID
```

区域2需要通过区域1到达区域0，所以要在区域1下，指向**对方RID**。

### **原理：LSA链路状态通告**

- 弄清楚：LSA由谁产生的、传播范围、包含了什么东西

LSA一共有11种，单播使用的LSA有6种类型

| 类别 | 说明        | 路由表中的协议 |
| ---- | ----------- | -------------- |
| 1    | 路由器LSA   | OSPF           |
| 2    | 网络LSA     | OSPF           |
| 3    | 网络汇总LSA | OSPF           |
| 4    | ASBR汇总LSA | OSPF           |
| 5    | AS外部LSA   | O_ASE          |
| 7    | NSSA外部LSA | O_NSSA         |

**LSA**(Link-State Advertisement)链路状态通告，路由条目的类型，只是路由报文的一个字段。
**LSACK**(Link-State Acknowledge)链路状态确认，路由报文。

```shell
# todo 重置路由器邻居关系
<R>reset ospf process
```

```shell
# todo 查看OSPF链路状态数据库——LSA1
<R1>display ospf lsdb router 
```

```shell
# todo 查看OSPF的LSDB链路状态数据库——LSA2
<R2>dis ospf lsdb network 
```

```shell
# todo 查看OSPF——LSA3
[R6]display ospf lsdb summary
```

```shell
# todo 查看OSPF——LAS4
[R6]show ip ospf border-routers   
[R1]show ip ospf database asbr-summary
```

```shell
# todo 查看OSPF——LSA5
[R6]display ospf lsdb ase
```

**同一个区域的LSDB(链路状态数据库)必须保持一致**，否则邻居关系无法建立

末梢网络：接口没选DR和BDR，且下面连接了一个终端设备PC

1类LSA：每个路由器都产生
2类LSA：DR产生
3类LSA：有区域间路由ABR产生，跨区域传播路由条目，只有路由条目信息，所有区域可以传递！
4类LSA：ABR产生，告诉ASBR该找谁，为5类LSA服务
5类LSA：ASBR产生
6类LSA：组播用
7类LSA：NSSA中的ASBR产生，仅NSSA区域传播，NSAA区域的ABR将7转5，让其他路由器学习

OSPF开销(向上取整) = 参考带宽(默认100M) / 接口带宽

<font color=#999AAA>交换机灯一亮一灭：</font>
<font color=#999AAA>原因：线过长或者水晶头做工不行，距离越长，带宽越低</font>
<font color=#999AAA>解决方法：所有路由器都修改参考带宽`bandwith-reference 1000`</font>

### **实验二：路由注入**

路由注入：让路由器能交换**不同的路由协议**的路由条目

重分发：
类型1：每经过一个路由器，开销cost会自动增加
类型2：不管经过几个路由器，开销cost恒定不变(为设定的值)，不管接口开销多大

```shell
# show 路由注入命令
[ospf-1]import-route rip 1 type 1 cost 50  设置路由引入RIP协议重分发类型1开销cost
[ospf-1]import-route rip 1 type 2 cost 50  设置路由引入RIP协议重分发类型2开销cost

[g0/0/0]ospf cost 10  设置路由器接口OSPF开销cost
```

- **STEP1：** 网络拓扑

<font color=#999AAA>注：这个拓扑不是上课的网络拓扑，这个实验也不是上课做的实验。</font>

<img src="./634c0bbe3fd44a1a961f736a8890d058.png">

- **STEP2：** 配置IP(略)

- **STEP3：** 配置路由注入

```shell
# todo AR5：进入RIP，配置OSPF引入
[AR5]rip
[AR5-rip-1]import-route ospf

# todo AR5：进入OSPF，配置RIP引入
[AR5]ospf
[AR5-ospf-1]import-route rip
```

ASBR：自治系统边界路由器
想要学习到对方路由条目，Adv rtr必须可达

### **实验三：路由汇总**

做了路由汇总之后，如果汇总的路由拓扑发生变化，也不影响

- **STEP1：** 网络拓扑

<img src="./5efac16d31e145a1881e2e3267c97dfe.png">

- **STEP2.1：** 配置环回地址

```shell
# todo AR4：配置环回地址
[AR4]int lo 0
[AR4-LoopBack0]ip add 14.1.4.1 255.255.255.0
[AR4-LoopBack0]ip add 14.1.5.1 255.255.255.0 sub
[AR4-LoopBack0]ip add 14.1.6.1 255.255.255.0 sub
[AR4-LoopBack0]ip add 14.1.7.1 255.255.255.0 sub
```

- **STEP2.2：** 宣告环回地址

```shell
# todo 宣告环回地址网段
[AR4-LoopBack0]ospf enable 1 a 0
```

- **STEP2.3：** 查看环回地址是否被学习到

```shell
# todo 查看路由表
[R3]dis ip rou
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 15       Routes : 15       

Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface

       12.1.1.0/24  RIP     100  1           D   23.1.1.2        GigabitEthernet0/0/0
       14.1.1.0/24  RIP     100  1           D   23.1.1.2        GigabitEthernet0/0/0
       14.1.4.1/32  RIP     100  1           D   23.1.1.2        GigabitEthernet0/0/0
       14.1.5.1/32  RIP     100  1           D   23.1.1.2        GigabitEthernet0/0/0
       14.1.6.1/32  RIP     100  1           D   23.1.1.2        GigabitEthernet0/0/0
       14.1.7.1/32  RIP     100  1           D   23.1.1.2        GigabitEthernet0/0/0
       ······
[R3]
```

可以看到掩码是32

- **STEP2.4：** 查看OSPF接口网络类型

```shell
# todo AR4：查看OSPF接口网络类型 
[AR4-LoopBack0]dis ospf int

         OSPF Process 1 with Router ID 14.1.1.4
                 Interfaces 

 Area: 0.0.0.1          (MPLS TE not enabled)
 IP Address      Type         State    Cost    Pri   DR              BDR 
 14.1.1.4        Broadcast    DR       1       1     14.1.1.4        14.1.1.1
 14.1.4.1        P2P          P-2-P    0       1     0.0.0.0         0.0.0.0
 
[AR4-LoopBack0]
```

可以看出环回地址是点到点网络类型

- **STEP2.5：** 修改OSPF接口网络类型

修改网络类型，让掩码变成24

```shell
# todo 修改网络类型
[R2]int lo 0
[R2-LoopBack0]ospf network-type broadcast  # 修改网络类型为广播，掩码变成24
```

华为：环回口认为是点到点P2P、点到多点p2mp网络类型，掩码是32；修改成广播Broadcast，掩码变成24

- **STEP3.1：** 汇总方法1

**同一个区域的LSDB必须保持一致**，所以只能在ABR或者ASBR上做汇总，而且是在要汇总的区域内进行汇总。

```shell
[R1]ospf 1
[R1-ospf-1]a 1                                  
[R1-ospf-1-area-0.0.0.1]abr-summary 14.1.4.0 255.255.252.0   
```

- **STEP3.2：** 查看路由表

```shell
[R3]dis ip rou
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 12       Routes : 12       

Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface

       12.1.1.0/24  RIP     100  1           D   23.1.1.2        GigabitEthernet0/0/0
       14.1.1.0/24  RIP     100  1           D   23.1.1.2        GigabitEthernet0/0/0
       14.1.4.0/22  RIP     100  1           D   23.1.1.2        GigabitEthernet0/0/0
       ······
[R3]
```

- **STEP4.1：** 汇总方法2

在汇总之前，先配置环回地址和路由引入。

```shell
# todo AR3：配置环回地址
[R3]int lo 0
[R3-LoopBack0]ip add 192.168.0.1 24
[R3-LoopBack0]ip add 192.168.1.1 24 sub
[R3-LoopBack0]ip add 192.168.2.1 24 sub
[R3-LoopBack0]ip add 192.168.3.1 24 sub

# 头都 AR3：宣告网段
[R3-LoopBack0]rip 1
[R3-rip-1]net
[R3-rip-1]network 192.168.0.0
[R3-rip-1]network 192.168.1.0
[R3-rip-1]network 192.168.2.0
[R3-rip-1]network 192.168.3.0
```

```shell
# todo AR2：路由引入
[R2]ospf 1
[R2-ospf-1]import-route rip
```

```shell
# todo AR1：查看路由表
<R1>dis ip rou
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 22       Routes : 22       

Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface
    ······
    192.168.0.0/24  O_ASE   150  1           D   12.1.1.2        GigabitEthernet0/0/0
    192.168.1.0/24  O_ASE   150  1           D   12.1.1.2        GigabitEthernet0/0/0
    192.168.1.0/26  Direct  0    0           D   192.168.1.1     LoopBack0
    192.168.1.1/32  Direct  0    0           D   127.0.0.1       LoopBack0
   192.168.1.63/32  Direct  0    0           D   127.0.0.1       LoopBack0
    192.168.2.0/24  O_ASE   150  1           D   12.1.1.2        GigabitEthernet0/0/0
    192.168.3.0/24  O_ASE   150  1           D   12.1.1.2        GigabitEthernet0/0/0
    ······
<R1>
```

```shell
# todo AR2：汇总
[R2-ospf-1]asbr-summary 192.168.0.0 255.255.252.0 cost 66  # ASBR汇总，设置开销为66
```

```shell
# todo AR1：查看路由表
<R1>dis ip rou
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 19       Routes : 19       

Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface
    ······
    192.168.0.0/22  O_ASE   150  66          D   12.1.1.2        GigabitEthernet0/0/0
    192.168.1.0/26  Direct  0    0           D   192.168.1.1     LoopBack0
    192.168.1.1/32  Direct  0    0           D   127.0.0.1       LoopBack0
   192.168.1.63/32  Direct  0    0           D   127.0.0.1       LoopBack0
   ······
<R1>
```

### **实验四：默认路由**

- **STEP1：** 网络拓扑

<img src="./30fff28111df4c24869e1f2d41d7e324.png">

- **STEP2：** 配置默认路由、下放路由

```shell
# todo AR1：配置默认路由
[R1]ip route-static 0.0.0.0 0.0.0.0 192.168.1.1
```

此时，如果下面路由器想配置默认路由，需要手动添加，比较麻烦，所以考虑使用OSPF下放一条默认路由：更新后的默认路由也能下放。

```shell
# todo AR1：OSPF下放路由
[R1-ospf-1]default-route-advertise
```

```shell
# todo AR2：查看路由表
[R2]dis ip rou
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 16       Routes : 16       

Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface
        0.0.0.0/0   O_ASE   150  1           D   12.1.
        ······
[R2]
```

默认路由通过OSPF(O_ASE)获取到

- **STEP3：** 把下放路由改成always查看现象

```shell
# todo AR1：首先删除默认路由
[R1]un ip route-static 0.0.0.0 0.0.0.0 192.168.1.1
[R1]un ip route-static 0.0.0.0 0.0.0.0 192.168.3.254
```

```shell
# todo AR2：查看路由表
[R2]dis ip rou
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 15       Routes : 15       

Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface
       10.0.1.1/32  OSPF    10   1           D   12.1.1.1        GigabitEthernet0/0/0
       10.0.2.0/24  Direct  0    0           D   10.0.2.2        LoopBack0
       10.0.2.2/32  Direct  0    0           D   127.0.0.1       LoopBack0
      ······
[R2]
```

```shell
# todo AR1：配置下放路由always
[R1]ospf 1
[R1-ospf-1]default-route-advertise always 
```

```shell
# todo AR2：查看路由表
[R2]dis ip rou
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 16       Routes : 16       

Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface
        0.0.0.0/0   O_ASE   150  1           D   12.1.1.1        GigabitEthernet0/0/0
       ······
[R2]
```

注：
1.默认路由可以把其他路由器的条目吸引到出口路由器
2.下放默认路由必须得有默认路由才能下放
3.always：不管有没有默认路由都下放

### **实验五：末梢网络**

- **STEP1：** 网络拓扑

<img src="./2f82af0675ac40ebb9430e4907964487.png">

- **STEP2.1：** 配置末梢网络(学习了3类LSA)

末梢网络：不接受4、5、7类LSA，只接受1、3类LSA。有效降低网络的复杂度。
只要在一个路由器上配置末梢网络，整个区域都要配置末梢网络

```shell
# todo AR2：配置末梢网络
[R2]ospf
[R2-ospf-1]a 1
[R2-ospf-1-area-0.0.0.1]stub
# 配置完该命令后，可以看到日志信息

# todo AR1：配置末梢网络
[R1]ospf
[R1-ospf-1]a 1
[R1-ospf-1-area-0.0.0.1]stub
# 配置完该命令后，ABR会主动产生一个默认路由下放给区域内路由器

# todo AR1：查看路由表的默认路由
[R1]dis ip rou
```

此时，

- **STEP2.2：** 配置末梢网络(不学习3类LSA)

```shell
# todo AR1：配置末梢网络不接受3类LSA
[R1-ospf-1-area-0.0.0.1]stub no-summary 

# todo AR2：配置末梢网络不接受3类LSA
[R2-ospf-1-area-0.0.0.1]stub no-summary 

# todo AR1：查看路由表
[R2]dis ip rou
# 可以看到除了直连，只剩一条默认路由，但是可以ping通其他区域路由
```

两个路由器都配置不接受3类LSA邻居关系才能建立
骨干区域不能做成末梢网络
末梢网络**不允许**在末梢区域**重分发**
但是 NSSA 可以做到末梢网络和重分发

- **STEP3.1：** 配置NSSA

```shell
# todo AR4：配置NSSA
[R4]ospf
[R4-ospf-1]a 2
[R4-ospf-1-area-0.0.0.2]nssa 

# todo AR3：配置NSSA
[R3]ospf
[R3-ospf-1]a 2
[R3-ospf-1-area-0.0.0.2]nssa
```

- **STEP3.2：** NSSA下放路由

正常NSSA不下放默认路由，下放默认路由可以配置以下命令

```shell
# todo AR3：配置NSSA下放路由
[R3-ospf-1-area-0.0.0.2]nssa default-route-advertise
```

如果想要不接受3类LSA，可以配置

```shell
# todo AR3：不接受3类LSA
[R3-ospf-1-area-0.0.0.2]nssa default-route-advertise no-summary

# todo AR4：不接受4类LSA
[R4-ospf-1-area-0.0.0.2]nssa default-route-advertise no-summary
```

## RIP

RIP属于IGP(内部网关协议)

### **原理：距离矢量路由协议**

距离矢量：只关心方向和度量

OSPF封装在IP中，协议号89
RIP封装在UDP中，目标端口号520

<img src="./7b841e84c947488f9aa30ec124342629.png">

注：
**1.** RIP每隔30s发一次更新，180s未应答就从路由表删除。
**2.** 更新原则——删除：发跳数为16的更新，然后将该路由条目从路由表中删除掉。
**3.** 避免路由环路方法：最大跳数、水平分割、路由毒化、毒性反转、触发更新

### **实验六：RIP版本**

<img src="./4b56590fe7a24986b4ae1a076b172025.png">

**RIPv2组播路由更新的目的地址使用`224.0.0.9`**

- **STEP1：** 网络拓扑

<img src="./0c885af960194cfa9eacc2d039cc4891.png">

- **STEP2.1：** 配置IP

```shell
# todo AR1：配置端口和环回地址
[AR1]int g0/0/0
[AR1-GigabitEthernet0/0/0]ip add 12.1.1.1 24  
[AR1-GigabitEthernet0/0/0]int lo 0
[AR1-LoopBack0]ip add 1.1.1.1 32

# todo AR2：配置端口和环回地址
[AR2]int g0/0/0
[AR2-GigabitEthernet0/0/0]ip add 12.1.1.2 24
[AR2-GigabitEthernet0/0/0]int lo 0
[AR2-LoopBack0]ip add 2.2.2.2 32
```

- **STEP2.2：** 配置版本和宣告

RIPv1只能按地址类别进行宣告。

```shell
# todo AR1：版本、宣告
[AR1]rip
[AR1-rip-1]version 1
[AR1-rip-1]network 1.0.0.0
[AR1-rip-1]network 12.0.0.0

# todo AR2：版本、宣告
[AR2]rip
[AR2-rip-1]version 1
[AR2-rip-1]network 2.0.0.0
[AR2-rip-1]network 12.0.0.0
```

- **STEP2.3：** 查看路由表

可以看到路由表中有对方接口和环回地址条目，且是有类路由。

- **STEP2.4：** 修改RIP版本

```shell
# todo AR1：版本
[AR1-rip-1]version 2

# todo AR2：版本
[AR2-rip-1]version 2
```

- **STEP2.5：** 查看路由表

可以看到路由表中的有类路由变成无类路由。

### **实验七：RIP相关配置**

- **STEP1：** 网络拓扑

<img src="./924ad19ecbdd497a93c903277172efcc.png">

- **STEP2.1：** 配置IP

路由器：

```shell
# todo AR1：配置IP
[AR3]int g0/0/0
[AR3-GigabitEthernet0/0/0]ip add 23.1.1.3 24
[AR3-GigabitEthernet0/0/0]int g0/0/1
[AR3-GigabitEthernet0/0/1]ip add 10.1.2.3 24

# todo AR2：配置IP
[AR2]int g0/0/0     
[AR2-GigabitEthernet0/0/0]ip add 12.1.1.2 24
[AR2-GigabitEthernet0/0/0]int g0/0/1
[AR2-GigabitEthernet0/0/1]ip add 23.1.1.2 24

# todo AR3：配置IP
[AR3]int g0/0/0
[AR3-GigabitEthernet0/0/0]ip add 23.1.1.3 24
[AR3-GigabitEthernet0/0/0]int g0/0/1
[AR3-GigabitEthernet0/0/1]ip add 10.1.2.3 24
```

| 主机 | IP地址     | 子网掩码      | 网关     |
| ---- | ---------- | ------------- | -------- |
| PC1  | 10.1.1.250 | 255.255.255.0 | 10.1.1.1 |
| PC2  | 10.1.2.250 | 255.255.255.0 | 10.1.2.3 |

- **STEP2.2：** 测试连通性

主机ping网关，路由器ping邻居，ping通即可

- **①配置：RIP基本配置**

- **STEP3：** 修改版本为RIPv1，宣告网段

```shell
# todo AR1：宣告网段
[AR1]rip
[AR1-rip-1]version 2
[AR1-rip-1]network 10.0.0.0
[AR1-rip-1]network 12.0.0.0

# todo AR2：宣告网段
[AR2]rip
[AR2-rip-1]version 2
[AR2-rip-1]network 12.0.0.0 
[AR2-rip-1]network 23.0.0.0

# todo AR3：宣告网段
[AR3]rip
[AR3-rip-1]version 2
[AR3-rip-1]network 23.0.0.0
[AR3-rip-1]network 10.0.0.0
```

- **②配置：路由汇总**

- **STEP4：** 配置汇总

RIPv1默认自动汇总，不能手动开启或关闭，RIPv2默认自动汇总，可以手动开启或关闭。

```shell
# todo STEP1：所有路由器改为RIPv2，开启自动汇总

# todo STEP2：查看AR2路由表，发现是没汇总的明细路由
[AR2-rip-1]dis ip rou
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 12       Routes : 12       

Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface

       10.1.1.0/24  RIP     100  1           D   12.1.1.1        GigabitEthernet0/0/0
       10.1.2.0/24  RIP     100  1           D   23.1.1.3        GigabitEthernet0/0/1
       ······
[AR2-rip-1]

# todo STEP3：所有路由器关闭自动汇总，路由表不变

# todo STEP4：所有路由器改为RIPv1，开启自动汇总

# todo STEP5：查看AR2路由表，发现汇总路由
[AR2-rip-1]dis ip rou
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 11       Routes : 12       

Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface

       10.0.0.0/8   RIP     100  1           D   12.1.1.1        GigabitEthernet0/0/0
                    RIP     100  1           D   23.1.1.3        GigabitEthernet0/0/1
       ······
[AR2-rip-1]

# todo STEP6：所有路由器关闭自动汇总，路由表不变
```

- **③配置：链路聚合**

- **STEP1：** 网络拓扑

<img src="./2affc9fb6a9b44178218ccef6cc2c94e.png">

- **STEP2：** 配置环回地址

```shell
# todo AR1：配置环回地址
[AR1]int lo 0
[AR1-LoopBack0]ip add 10.2.0.1 24
[AR1-LoopBack0]ip add 10.2.1.1 24 sub
[AR1-LoopBack0]ip add 10.2.2.1 24 sub
[AR1-LoopBack0]ip add 10.2.3.1 24 sub
```

- **STEP3.1：** 修改所有路由器为RIPv2
- **STEP3.2：** 配置链路聚合

```shell
# todo AR1配置链路聚合
[AR1]int g0/0/0
[AR1-GigabitEthernet0/0/0]rip summary-address 10.2.0.0 255.255.252.0
```

- **STEP3.3：** 查看路由表

```shell
# todo 查看AR2路由表
[AR2]dis ip rou
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 13       Routes : 13       

Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface
       10.1.1.0/24  RIP     100  1           D   12.1.1.1        GigabitEthernet0/0/0
       10.1.2.0/24  RIP     100  1           D   23.1.1.3        GigabitEthernet0/0/1
       10.2.0.0/22  RIP     100  1           D   12.1.1.1        GigabitEthernet0/0/0
       ······
[AR2-rip-1]
```

- **④配置：下放路由**

- **STEP1：** 网络拓扑

<img src="./c8aa4658042942c88cf32cfcc8d21c49.png">

- **STEP2：** 配置云

绑定信息：1.UDP、2.以太网
出端口：1
入端口：2
双向通道：√

- **STEP3：** 配置下放默认路由

```shell
# todo AR2：配置下放默认路由
[AR2-rip-1]default-route originate 
```

- **STEP4：** 查看路由表

查看AR1、AR3路由表，可以看到默认路由的条目：
`0.0.0.0/0   RIP     100  1           D   12.1.1.2        GigabitEthernet0/0/0`
`0.0.0.0/0   RIP     100  1           D   23.1.1.2        GigabitEthernet0/0/0`

- **⑤配置：抑制接口**

抑制接口：只收不发，一般连接终端接口配置抑制接口

- **STEP1：** 网络拓扑(同上)

- **STEP2：** 配置抑制接口

```shell
# todo AR1：设置抑制接口
[AR1-rip-1]silent-interface g0/0/1
# [AR1-rip-1]silent-interface all  # 把所有端口设置为抑制接口
```

实际上，可以先把所有端口设置为抑制接口，然后再取消其中某个需要的接口

```shell
# show 该配置模拟器上不可用，实际中可用
[AR1-rip-1]silent-interface all
[AR1-rip-1]un silent-interface g0/0/0
```

- **⑥配置：认证**

认证都是在接口下配置的

- **STEP1：** 网络拓扑(同上)

- **STEP2：** 配置认证

```shell
# todo AR1：配置认证
[AR1]int g0/0/0
[AR1-GigabitEthernet0/0/0]rip authentication-mode md5 usual cipher 123456

# todo AR2：配置认证
[AR2]int g0/0/0
[AR2-GigabitEthernet0/0/0]rip authentication-mode md5 usual cipher 123456
```

**密码不要加空格！！！**

## 使用技巧

使用SecureCRT连接eNSP设备：[CRT(Securecrt)连接ENSP设备](https://blog.csdn.net/qq_41490561/article/details/104894891)
