---
title: ICT战队-第3次笔记
subtitle: 
summary: VLAN技术 DHCP协议 STP协议
description: VLAN技术 DHCP协议 STP协议
keyworks: 
author: hhhhmdzz
language: zh-Hans
timezone: Asia/Shanghai
date: 2022-06-29 10:49:44
categories: 
- ICT战队数据通信
tags: 
- HCIA
- 华为eNSP
mathjax: true
---

# ICT战队-第3次笔记

[toc]

## 数据中心IDC机房

**1.光纤接口**：

SC：大方
LC：小方(交换机上的光模块基本上插的都是LC接头)
FC、ST：一般运营商用

<img src="./03 光纤接口-16564712016121.png">

**2.光纤跳线**：

多模光纤(青色)、单模光纤(黄色)
<img src="./03 光纤跳线-16564712016142.png">

OM2
OM3，蓝色
OM4，紫色，传输带宽最大

**3.光纤模块**：

<img src="./03 光纤模块-16564712016143.png">

基带宽带

## VLAN技术原理与配置

<font color=#999AAA>VLAN虚拟局域网：逻辑上把交换机分成两个交换机，一个VLAN等于一个广播域，等于一个网段。</font>目的是为了隔离广播域。

<br>

### 实验一：单个交换机相同VLAN间的通信

<br>

- **Step1：** 画出网络拓扑图

<img src="./03 实验一——网络拓扑-16564712016144.png">

```shell
# todo 查看Huawei交换机默认VLAN信息
<Huawei>display vlan  # 查看所有VLAN信息
<Huawei>display port vlan  # 查看各个接口类型
```

<font color=#999AAA>1.华为设备上默认有一个VLAN，为VLAN1
2.电脑的VLAN不是电脑的配置，而是电脑连接的端口对应的VLAN</font>

- **Step2：** 配置主机IP地址和子网掩码

| 主机 |  IP地址  |   子网掩码    |
| :--: | :------: | :-----------: |
| PC1  | 10.1.1.1 | 255.255.255.0 |
| PC2  | 10.1.1.2 | 255.255.255.0 |
| PC3  | 10.1.2.3 | 255.255.255.0 |

- **Step3：** 创建VLAN

```shell
# todo 配置VLAN
[Huawei]vlan 2  # 创建VLAN
[Huawei]undo vlan 2  # 删除VLAN
[Huawei-vlan2]describtion something  # 对VLAN添加描述
[Huawei]vlan batch 2 to 10  # 批量创建VLAN
```

一个交换机上最多可以创建4095个VLAN

- **Step4：** 把端口加入到创建的VLAN

法1：直接在VLAN模式下添加(**前提**：把端口改成access模式)

```shell
# todo 错误示范
[Huawei]vlan 2
[Huawei-vlan2]port GigabitEthernet 0/0/3
Error: Trunk or Hybrid port(s) can not be added or deleted in this manner.
[Huawei-vlan2]
```

法2：在端口试图下将端口划入对应的VLAN

```shell
# todo 进入交换机g0/0/3接口
[Huawei]inter g0/0/3
[Huawei-GigabitEthernet0/0/3]port link-type ?
  access        Access port
  dot1q-tunnel  QinQ port
  hybrid        Hybrid port
  trunk         Trunk port
# todo 指定端口类型，将交换机接口类型设置为接入模式
[Huawei-GigabitEthernet0/0/3]port link-type access

# todo 应用相应的VLAN到该端口上，将某个接口加入VLAN
[Huawei-GigabitEthernet0/0/3]port default vlan 2
```

| 端口类型 |             使用说明             |
| :------: | :------------------------------: |
| `access` | 交换机连终端设备(网络电视、PC……) |
| `trunk`  |          交换机连交换机          |
| `hybrid` |             混合类型             |

- **Step5：** 使用其中一个VLAN(冲突域)的主机ping交换机内**其他**VLAN(冲突域)的主机即可ping通

### **实验二：不同交换机相同VLAN间的通信**

- **一、使用两根线实现VLAN**

- **Step1：** 画出网络拓扑图(书接上回:smile:)

<img src="./03 实验二——网络拓扑1-16564712016146.png">

- **Step2：** 在两台交换机上各自配置VLAN(把某一条线路的端口加入创建的VLAN)

交换机LSW1的配置：

```shell
# todo 配置LSW1的VLAN
[Huawei]inter g0/0/4  # 进入端口
[Huawei-GigabitEthernet0/0/4]port link-type access  # 指定端口类型
[Huawei-GigabitEthernet0/0/4]port default vlan 2  # todo 应用相应的VLAN到该端口上，将某个接口加入VLAN
```

交换机LSW2的配置：

```shell
# todo 配置LSW2的VLAN
[Huawei]inter g0/0/2  # 进入端口
[Huawei-GigabitEthernet0/0/2]port link-type access  # 指定端口类型
[Huawei-GigabitEthernet0/0/2]port default vlan 2  # todo 应用相应的VLAN到该端口上，将某个接口加入VLAN
[Huawei-GigabitEthernet0/0/2]inter g0/0/3  # 进入端口
[Huawei-GigabitEthernet0/0/3]port link-type access  # 指定端口类型
[Huawei-GigabitEthernet0/0/3]port default vlan 2  # todo 应用相应的VLAN到该端口上，将某个接口加入VLAN
```

- **Step3.5：** 关闭STP生成树协议

<font color=#999AAA>由于生成树阻塞的问题，冲突域之内的主机无法ping通，所以要关闭Huawei交换机的STP生成树协议。</font>

```shell
[Huawei]display stp  # 显示STP信息
[Huawei]display stp brief  # 显示STP简要信息
[Huawei]undo stp enable  # 关闭STP生成树协议
Warning: The global STP state will be changed. Continue? [Y/N]y
Info: This operation may take a few seconds. Please wait for a moment...done.
[Huawei]
```

- **Step4：** 使用其中一个交换机的一个VLAN(冲突域)的主机ping其他交换机内**同一个**VLAN(冲突域)的主机即可ping通

- **二、使用一根线(通过标签管理)实现VLAN**

<img src="./03 VLAN标签-16564712016145.png">

UT：untagged 不打标
TG：tagged 打标

- **Step1：** 画出网络拓扑图(书接上回:smirk:)

<img src="./03 实验二——网络拓扑2-16564712016147.png">

- **Step2：** 在两台交换机上各自配置VLAN

注：
**1.** 需要一层一层的来删除配置，直到恢复到默认的配置。否则会出现错误：`Error: Please renew the default configurations.`
**2.** 或者直接删除线路直接配置。

交换机LSW1的配置：

```shell
# todo 配置LSW1的VLAN(删除线路)
[Huawei]inter g0/0/4  # 进入端口
[Huawei-GigabitEthernet0/0/4]port link-type trunk  # 指定端口类型
[Huawei-GigabitEthernet0/0/4]port trunk allow-pass vlan 2  # 设置端口允许通过的VLAN
# [Huawei-GigabitEthernet0/0/4]port trunk allow-pass vlan all  # 设置所有端口允许通过的VLAN
```

交换机LSW2的配置：

```shell
# todo 配置LSW2的VLAN(删除配置)
[Huawei]inter g0/0/3  # 进入端口
[Huawei-GigabitEthernet0/0/3]undo port default vlan
[Huawei-GigabitEthernet0/0/3]undo port link-type
[Huawei-GigabitEthernet0/0/3]port link-type trunk  # 指定端口类型
[Huawei-GigabitEthernet0/0/3]port trunk allow-pass vlan 2  # 设置端口允许通过的VLAN
# [Huawei-GigabitEthernet0/0/3]port trunk allow-pass vlan all  # 设置所有端口允许通过的VLAN
```

- **Step3：** 使用其中一个交换机的一个VLAN(冲突域)的主机ping其他交换机内**同一个**VLAN(冲突域)的主机即可ping通

- **Step4.1：** 抓包测试(开始抓包即可查看数据帧信息)

注：
**1.** trunk封装的标签802.1Q(思科有私有的协议ISL)
**2.** 本征VLAN(Navtive Vlan)，只要不打标的帧就是转发到本征VLAN的帧。
<font color=#999AAA>例如，如果 VLAN 99 被配置为本征 VLAN，则PVID为 99，那么所有的无标记流量都被转发到 VLAN 99。</font>
**3.** 所以，两个直连的交换机端口的本征VLAN必须相同
**4.** Huawei真机上可以同时进两个端口进行相同的操作

- **三、使用三个交换机一根线实现VLAN**

<font color=#999AAA>交换机之间要通，要设置**相同**的VLAN。</font>

- **Step1：** 画出网络拓扑图(书接上回:smile:)

<img src="./03 实验二——网络拓扑3-16564712016148.png">

- **Step2：** 在三台交换机上各自配置VLAN

交换机LSW1和LSW2的配置：略

交换机LSW3的配置：

```shell
# todo 配置LSW2的VLAN
[Huawei]vlan 2
[Huawei-vlan2]inter g0/0/2  # 进入端口
[Huawei-GigabitEthernet0/0/2]port link-type trunk  # 指定端口类型
[Huawei-GigabitEthernet0/0/2]port trunk allow-pass vlan 2  # 设置端口允许通过的VLAN
# [Huawei-GigabitEthernet0/0/2]port trunk allow-pass vlan all  # 设置端口允许通过的VLAN
[Huawei-GigabitEthernet0/0/2]inter g0/0/3  # 进入端口
[Huawei-GigabitEthernet0/0/3]port link-type trunk  # 指定端口类型
[Huawei-GigabitEthernet0/0/3]port trunk allow-pass vlan 2  # 设置端口允许通过的VLAN
# [Huawei-GigabitEthernet0/0/3]port trunk allow-pass vlan all  # 设置端口允许通过的VLAN
```

- **Step3：** 使用其中一个交换机的一个VLAN(冲突域)的主机ping其他交换机内**同一个**VLAN(冲突域)的主机即可ping通

### **实验三：VLAN间路由(单臂路由)**

通过路由器转发不同网段的数据。

- **Step1：** 画出网络拓扑图(书接上回:smirk:)

<font color=#999AAA>路由器AR1选AR2220</font>
<img src="./03 实验三——网络拓扑-16564712016149.png">

- **Step2：** 配置交换机的VLAN

把交换机的接口接入模式全部设置成trunk。

连接主机的交换机的接口：略

连接路由器的交换机的接口：

```shell
# todo 设置交换机接口模式为trunk
[Huawei]inter g0/0/4
[Huawei-GigabitEthernet0/0/4]port link-type trunk
[Huawei-GigabitEthernet0/0/4]port trunk allow-pass vlan all
```

- **Step3：** 配置路由器

<font color=#999AAA>路由器：用来转发不同网段的设备</font>

<font color=#999AAA>在本实验中：</font>
VLAN1 要通信，在物理接口上配IP即可
VLAN2 要通信，则要用打标签的方法

```shell
# todo 配置路由器
# todo 配置g0/0/0接口
[Huawei]inter g0/0/0  # 进入接口
[Huawei-GigabitEthernet0/0/0]ip address 10.1.1.254 24  # 配置IP
[Huawei-GigabitEthernet0/0/0]display this  # 显示配置信息
[V200R003C00]
#
interface GigabitEthernet0/0/0
 ip address 10.1.1.254 255.255.255.0 
#
return
[Huawei-GigabitEthernet0/0/0]

# todo 配置g0/0/0.2子接口(逻辑接口)
[Huawei-GigabitEthernet0/0/0]inter g0/0/0.2  # 进入接口
[Huawei-GigabitEthernet0/0/0.2]dot1q termination vid 2  # 封装dot1q协议，该子接口对应VLAN2，该接口收发的数据帧都要打上标签2
[Huawei-GigabitEthernet0/0/0.2]ip address 10.1.2.254 24  # 添加IP
[Huawei-GigabitEthernet0/0/0.2]arp broadcast enable  # 开启终结子接口的ARP广播功能
[Huawei-GigabitEthernet0/0/0.2]display this  # 显示配置信息
[V200R003C00]
#
interface GigabitEthernet0/0/0.2
 dot1q termination vid 2
 ip address 10.1.2.254 255.255.255.0 
 arp broadcast enable
#
return
[Huawei-GigabitEthernet0/0/0.2]
```

注：
**1.** 物理接口数据帧不打标，逻辑接口数据帧才打标。(逻辑接口依赖物理接口通信)
**2.** g0/0/0.1和VLAN1没有对应关系，g0/0/0.x随便用。
**3.** **跨VLAN通信过程**！！！

- **Step3.5：** 主机配置相应的网关

- **Step4：** 使用其中一个VLAN(冲突域)的主机ping其他VLAN(冲突域)的主机即可

### **实验四：VLAN间路由(三层交换)**

- **一、使用两个交换机实现VLAN间通信(三层交换机之间的VLAN路由)**

- **Step1：** 画出网络拓扑图(书接上回:smile:)

<img src="./03 实验四——网络拓扑1-165647120161410.png">

- **Step1.5：** 配置主机的IP地址、子网掩码、网关(略)

- **Step2：** 配置交换机的VLAN

为交换机端口划分VLAN：略

为交换机接口配置IP：

```shell
# todo 为交换机接口配置IP
[Huawei-Vlanif2]int vlan 1
[Huawei-Vlanif1]ip add 10.1.1.254 24
[Huawei-Vlanif1]int vlan 2
[Huawei-Vlanif2]ip add 10.1.2.254 24
[Huawei-Vlanif2]
```

- **Step2.5：** 主机配置网关
- **Step3：** 使用其中一个VLAN的主机ping其他VLAN的主机即可
- **Step4：** 抓包

经过交换机转发的数据帧**不打**标签，因为其连接各主机是 **access** 模式。

## DHCP原理与配置

- **一、使用交换机配置DHCP和DNS**

- **Step1：** 画出网络拓扑图(书接上回:smirk:)

<img src="./03 DHCP实验1——网络拓扑-165647120161411.png">

- **Step2：** 配置三层交换机的DHCP和DNS服务

**法1：** 基于接口DHCP配置

```shell
# todo 配置DHCP和DNS
[Huawei]dhcp enable  # 开启DHCP服务
Info: The operation may take a few seconds. Please wait for a moment.done.
[Huawei]inter vlan 1  # 进入VLAN1
[Huawei-Vlanif1]dhcp select interface  # 使用接口的IP作为网关
[Huawei-Vlanif1]dhcp server lease day 0 hour 2 minute 0  # 设置DHCP租约时间
[Huawei-Vlanif1]dhcp server dns-list 114.114.114.114  # 设置DNS服务器地址114.114.114.114(万能DNS地址)
[Huawei-Vlanif1]
```

**法2：** 基于DHCP地址池分配全局配置

```shell
# todo 配置DHCP和DNS
[Huawei]ip pool 1  # 创建IP地址池
Info:It's successful to create an IP address pool.
[Huawei-ip-pool-1]network 10.1.2.0 mask 24  # 配置网段和掩码
[Huawei-ip-pool-1]gateway-list 10.1.2.254  # 配置网关
[Huawei-ip-pool-1]dns-list 114.114.114.114  # 设置DNS地址
[Huawei-ip-pool-1]lease day 0 hour 2 minute 0  # 设置DHCP租约时间
[Huawei-ip-pool-1]excluded-ip-address 10.1.2.200 10.1.2.250  # 这些地址不会被分配出去
[Huawei-ip-pool-1]inter vlan 2  # 进入VLAN2
[Huawei-Vlanif2]dhcp select global  # 设置DHCP基于全局配置
```

注：
**1.** DHCP获取的地址有祖约时间
**2.** 给别人分祖约时间一般分2h
**3.** 一般过了租约时间的2/3，PC会发送请求，服务器会继续续租

- **Step3：** 主机开启并应用DHCP配置，即可获得DHCP自动分配的IP地址

- **二、使用DHCP中继配置DHCP和DNS**

- **Step1：** 画出网络拓扑图(书不接上回)

<img src="./03 DHCP实验2——网络拓扑-165647120161512.png">

- **Step2：** 配置路由器的DHCP和DNS服务

配置路由器**AR2**：

```shell
# todo 配置接口IP、静态路由
[Huawei]dhcp enable  # 开启DHCP服务
[Huawei]int g0/0/0  # 进入接口
[Huawei-GigabitEthernet0/0/0]ip add 23.1.1.3 24  # 添加IP地址、掩码
[Huawei-GigabitEthernet0/0/0]q
[Huawei]ip route-static 0.0.0.0 0.0.0.0 23.1.1.2  # 缺省路由，所有网络到达所有网络的路径都是通过23.1.1.2这个地址转发的

# todo 创建DHCP地址池分配IP
[Huawei-GigabitEthernet0/0/0]ip pool pc  # 创建IP地址池
Info: It's successful to create an IP address pool.
[Huawei-ip-pool-pc]network 192.168.1.0 mask 255.255.255.0  # 设置网段、掩码
[Huawei-ip-pool-pc]gateway-list 192.168.1.1  # 设置网关
[Huawei-ip-pool-pc]dns-list 114.114.114.114  # 设置DNS地址
[Huawei-ip-pool-pc]dis th  # 显示本配置
[V200R003C00]
#
ip pool pc
 gateway-list 192.168.1.1 
 network 192.168.1.0 mask 255.255.255.0 
 dns-list 114.114.114.114 
#
return

# todo 启用DHCP
[Huawei-ip-pool-pc]inter g0/0/0  # 进入接口
[Huawei-GigabitEthernet0/0/0]dhcp select global  # 设置DHCP全局配置
[Huawei-GigabitEthernet0/0/0]dis th  # 显示本配置
[V200R003C00]
#
interface GigabitEthernet0/0/0
 ip address 23.1.1.3 255.255.255.0 
 dhcp select global
#
return
[Huawei-GigabitEthernet0/0/0]
```

配置路由器**AR1**：

```shell
# todo 配置IP地址
[Huawei]inter g0/0/1  # 进入接口
[Huawei-GigabitEthernet0/0/1]ip add 23.1.1.2 24  # 添加IP
[Huawei-GigabitEthernet0/0/1]ping 23.1.1.3  # 测试网络连通性
  PING 23.1.1.3: 56  data bytes, press CTRL_C to break
    Reply from 23.1.1.3: bytes=56 Sequence=1 ttl=255 time=100 ms
    Reply from 23.1.1.3: bytes=56 Sequence=2 ttl=255 time=20 ms
    Reply from 23.1.1.3: bytes=56 Sequence=3 ttl=255 time=30 ms
    Reply from 23.1.1.3: bytes=56 Sequence=4 ttl=255 time=30 ms

  --- 23.1.1.3 ping statistics ---
    4 packet(s) transmitted
    4 packet(s) received
    0.00% packet loss
    round-trip min/avg/max = 20/45/100 ms

# todo DHCP中继
[Huawei]dhcp enable  # 开启DHCP服务
Info: The operation may take a few seconds. Please wait for a moment.done.
[Huawei]inter g0/0/0  # 进入接口
[Huawei-GigabitEthernet0/0/0]ip add 192.168.1.1 24  # 添加IP地址
[Huawei-GigabitEthernet0/0/0]dhcp select relay
[Huawei-GigabitEthernet0/0/0]dhcp relay server-ip 23.1.1.3  # 指向DHCP服务器IP
```

AR2根据源IP地址判断应该用哪一个地址池分配IP地址
广播包->单播包

- **Step3：** 抓包测试(略)

## STP原理与配置

STP作用：消除环路、阻塞端口、链路备份

<img src="../../../STDATA/VSCodeData/MarkDown/ICT战队/ICT数通/03/03 STP作用——消除环路.png">
<img src="./03 STP作用——阻塞端口-165647120161514.png">
<img src="../../../STDATA/VSCodeData/MarkDown/ICT战队/ICT数通/03/03 STP作用——链路备份.png">

### 环路引起的问题

**交换机转发流程**：交换机收到数据帧，先把源MAC添加到MAC表中，然后根据目标MAC转发 。如果MAC表中没有目的MAC则泛洪(把帧复制成多份，向除了接收端口外其他**相同VLAN**下的所有接口以及**trunk接口**转发单播帧)。

<img src="./03 交换机转发流程-165647120161516.png">

**问题**：

**环路引起的问题之一——广播风暴**.
<img src="./03 广播风暴-165647120161517.png">

**环路引起的问题之二——MAC地址表不稳定**.
<img src="./03 MAC地址表不稳定-165647120161518.png">

**环路引起的问题之三——帧的多个副本**.

### 生成树基本计算过程

- Step1. 选取根桥/根交换机(一个广播域内桥ID<font color=#999AAA>(桥ID由网桥优先级和MAC地址组成)</font>最小的交换机)

```shell
[Huawei]dis stp  # 查看交换机STP信息
[Huawei]dis stp brief  # 查看交换机STP信息
[Huawei]stp priorit 4096  # 修改交换机优先级
```

**1.** 优先级步长 4096
**2.** Huawei 交换机端口开销默认为 2000

<img src="./03 交换机端口角色-165647120161519.png">

- Step2. **Root Port** 根端口(**非根**交换机到达**根**交换机累计路径开销最小的接口)

根端口RP比较优先级：

1) 根桥ID大小
2) 到达根桥累计路径开销
3) 指定桥ID/**发送者**桥ID
4) 指定PID(端口ID：port id)/**发送者**端口ID = 端口优先级(默认128) + 端口号
5) **本地**端口ID

注：
**1.** **根桥**上的端口**不是**根端口！！！
**2.** **根端口**是在**非根桥**上的端口！！！
**3.** 根桥确定之前，交换机只能发送BPDU，不能转发BPDU
**4.** 一旦根桥被选出来，非根桥没有权利发送BPDU

转发：不改动数据帧
中继：改动数据帧，然后转发

- Step3. **Designated Port** 指定端口(一个网段/冲突域内到达根桥累计开销最小的接口)

一个**冲突域**就要选一个**指定端口**

指定端口比较优先级：

1) 到达根桥累计开销
2) 指定桥ID

- Step4. **Alternate Port** 非指定/预备端口(非根交换机上除根端口和指定端口外的其他端口)

<img src="./03 端口状态描述-165647120161520.png">

**Discarding** = Blocking + Listening

**端口状态变换：**
<img src="./03 端口状态变换-165647120161521.png">

**Blocking：** 只收BPDU
如果要维持Blocking，那么20s内就要收到根桥发送的配置BPDU。如果连续20s没收到根桥发送的配置BPDU，就自动从Blocking进入Listening状态。或者其他接口断掉直接进入Listening状态。
**Listening：** 收发BPDU
该状态下，如果发现自己是非指定端口，那么将该端口转为Blocking状态，如果发现自己是根桥端口，再等15s，此时还是Listening状态(能收发配置BPDU，但是不能收发用户数据，因为要通过配置BPDU判断自己的端口角色)。如果发现自己真的是根桥端口，那么会变成Learning(Learning再持续15s变成Forwarding)。
**Learning:** 收发BPDU，只收用户数据(不转发数据帧、学习MAC地址表)
**Forwarding：** 收发BPDU，收发用户数据(转发数据帧、学习MAC地址表)

注：
**1.** 一个老化时间 20s，最大转发延迟 15s
**2.** 一个端口从 Blocking 到 Forwarding 所需最大时间 = 20s(老化) + 15s(Listening) + 15s(Learning) = 50s
**3.** MAC地址表快速老化，老化时间缩短

>**问题：** 左右两根线断掉之后，LSW1的端口GE 0/0/2从Blocking到Forwarding需要多长时间？

<img src="./03 生成树问题-165647120161522.png">

>**答：**
>左线断30s = 0s(LSW1：Blocking->Listening) + 15s(LSW1：Listening->Learning) + 15s(LSW1：Learning->Forwarding)
>右线断50s = 20s(LSW1：Blocking->Listening) + 15s(LSW1：Listening->Learning) + 15s(LSW1：Learning->Forwarding)

<br>

### 生成树简单配置

```shell
[sw]stp enable
[sw]stp mode stp
[sw]stp priority 8192
```

<font color=#999AAA>资料：《H3C STP技术白皮书》</font>
