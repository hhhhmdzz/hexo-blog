---
title: ICT战队-第1次笔记
subtitle: 
summary: TCP/IP通信基础 eNSP基础
description: TCP/IP通信基础 eNSP基础
keyworks: 
author: hhhhmdzz
language: zh-Hans
timezone: Asia/Shanghai
date: 2022-06-29 10:47:11
categories: 
- ICT战队数据通信
tags: 
- HCIA
- 华为eNSP
mathjax: true
---

# ICT战队-第1次笔记

[toc]

## IP通信基础

数据流方向：单工、半双工、全双工

<font color=#999AAA>总线型网络带宽共享</font>

网络拓扑图：路由器、PC端、(忽略这个)网络
网络拓扑图画图要求：标上设备端口、广域网线路和局域网线路之间要有线缆颜色区分

<img src='./01 常见网络拓扑架构.png'>

<font color=#999AAA>无线路由器的加密方式：
aes(advanced encryption standard高级加密标准)
tkip(temporal key integrity protocol暂时密钥集成协议)
(现普遍采用目前最安全的加密方式WPA2进行加密)</font>

<img src='./01 局域网特点和常用设备.png'>

<font color=#999AAA>防火墙：端口与端口之间通信的安全策略</font>

<img src='./01 IP网络典型结构.png'>

三层架构：核心层、汇聚层、接入层

`GE 0/0/0`

`GE`当前端口的带宽

`et 0/0/0`十兆带宽
`fa 0/0/0`百兆带宽
`Ge 0/0/0`千兆带宽
`xg 0/0/0`万兆带宽

<font color=#999AAA>查看端口带宽指令`display`、`show`</font>

`0/0/0`设备堆叠编号/模块号/

## TCP/IP基础

### TCP/IP协议栈

<img src="./01 OSI七层功能.png">

- **问题**：路由器连着一台电脑，网络正常，中间加个交换机，网络不同了，原因？

- **答**：vlan划分有问题，端口隔离有问题，MAC地址表······

```shell
# 电脑基础配置
IP地址：192.168.1.66
子网掩码：255.255.255.0
网关：192.168.1.1
# 路由器
<Huawei>sys
Enter system view, return user view with Ctrl+Z.
[Huawei]inter g0/0/0
[Huawei-GigabitEthernet0/0/0]ip add 192.168.1.1 24
[Huawei-GigabitEthernet0/0/0]
# 电脑
PC>ping 192.168.1.1

Ping 192.168.1.1: 32 data bytes, Press Ctrl_C to break
From 192.168.1.1: bytes=32 seq=1 ttl=255 time=157 ms
From 192.168.1.1: bytes=32 seq=2 ttl=255 time=46 ms
From 192.168.1.1: bytes=32 seq=3 ttl=255 time=32 ms
From 192.168.1.1: bytes=32 seq=4 ttl=255 time=16 ms
From 192.168.1.1: bytes=32 seq=5 ttl=255 time=46 ms

--- 192.168.1.1 ping statistics ---
  5 packet(s) transmitted
  5 packet(s) received
  0.00% packet loss
  round-trip min/avg/max = 16/59/157 ms

PC>
# 交换机
<Huawei>display mac-address
MAC address table of slot 0:
-------------------------------------------------------------------------------
MAC Address    VLAN/       PEVLAN CEVLAN Port            Type      LSP/LSR-ID  
               VSI/SI                                              MAC-Tunnel  
-------------------------------------------------------------------------------
00e0-fca4-15de 1           -      -      GE0/0/1         dynamic   0/-         
5489-9889-0ff0 1           -      -      GE0/0/2         dynamic   0/-         
-------------------------------------------------------------------------------
Total matching items on slot 0 displayed = 2 

<Huawei>
```

交换机收到数据帧后，不管有没有MAC地址，记录数据帧的MAC地址，如果MAC地址表有这个MAC地址，则覆盖

>**干坏事**：如何获取同一个局域网内其他主机的上网信息
>可以用macof直接把交换机MAC地址打满，这样就可以把交换机变成集线器(用垃圾MAC信息把交换表打满，这样交换机就无法学习正常交换帧的地址，所以此后遇到正常的单播帧就全部封站)
>**安全防护**：MACFlour，设置特定的MAC地址的学习记录，只要发送超过这个设置的阈值，就不接受MAC地址

不同服务对应的端口：



|  协议  |  端口  |
| :----: | :----: |
|  FTP   |   21   |
|  Ssh   |   22   |
| Telnet |   23   |
|  FTP   | 20、21 |
|  SMTP  |   25   |
|  TFTP  |   69   |
|  HTTP  |   80   |
| HTTPS  |  443   |



<font color=#999AAA>20：进行数据传输、21：传输控制</font>



|     层     | PDU  |
| :--------: | ---- |
|   物理层   | 位   |
| 数据链路层 | 帧   |
|   网络层   | 包   |
|   传输层   | 段   |
|   应用层   | 数据 |

发数据包 -> 封装
收数据包 -> 解封

IP数据包：
ID：数据片在传输过程中的位置
MTU：数据包最大限制，默认1500Byte
Flags：强制不分片用来检测MTU
Fragement Offset：
TTL(Time ti Live)：`trancrt`指令用来查询路由器节点
Protocol：(防火墙设置，限制某个协议)

### TCP/IP协议栈报文封装

<img src="01 TCP首部格式.png">

<img src="01 IP数据报.png">

帧头特点：
IP报头：源地址在前，目标地址在后
数据帧：目标地址在前，源地址在后

>**干坏事**：ARP中间人攻击(ARP欺骗)，在局域网中截获别人数据包。(断别人的网)

ARP协议：
首先查表有无对应的目的MAC地址，有直接封装数据帧并发送给交换机，无则使用ARP协议查询MAC地址。
广播(发送ARP请求，询问对方MAC地址)->单播(返回MAC地址)

二层广播地址是全F，ARP不封装IP地址

>**干坏事**：获取局域网内其他主机权限，首先对目标主机做中间人欺骗，然后做DNS欺骗，目标主机打开任何网站均解析到我们搭建的一个网站上面，然后让对方点击网站内容然后运行。
>**安全防护**：找到vlan网关的物理MAC地址和IP地址，再做静态IP绑定，就可以绕过ARP中间人攻击

## 传输层协议

<font color=#999AAA>TCP半连接攻击：
流量清洗：</font>

- TCP可靠性：
  - 面向连接的传输
  - 最大报文段长度
  - 传输确认机制
  - 首部和数据的检验和
  - 流量控制

| 0-65535端口号 | 说明       |
| :-----------: | ---------- |
|    0-1023     | 熟知端口号 |
|  1024-49151   | 登记端口号 |
|  49152—65536  | 短暂端口号 |

TCP连接建立：TCP三次握手

<img src="./01 TCP连接的建立.png">

TCP连接释放：TCP四次握手

<img src="./01 TCP连接的终止.png">

Acknowledgment Number：确认帧(下次希望收到的序列号)
Syn：建立连接
Fin：断开连接

TCP确认机制：显示确认
OSPF确认机制：隐式确认

>**搞事情**：web渗透，开代理，通过**拦截数据包，修改数据包的参数

**私有地址**：
10.0.0.0 ~ 10.255.255.255
172.16.0.0 ~ 172.31.255.255
192.168.0.0 ~ 192.168.255.255(尽量别用)

|  二进制  | 十进制 |
| :------: | :----: |
| 10000000 |  128   |
| 11000000 |  192   |
| 11100000 |  224   |
| 11110000 |  240   |
| 11111000 |  248   |
| 11111100 |  252   |

公式：$2^n-2 > w$

50：192.168.2.0/26
29：192.168.2.64/27
2：192.168.2.96/30

<font color=#999AAA>实际中一般使用24个24个地用</font>

## VRP基础及操作

- 要求：
  - 了解VRP体系结构
  - 掌握VRP配置基础
  - 掌握升级路由器软件和备份配置文件的方法

### 1. VRP概述

通用路由平台VRP(Versatile Routing Platform)是华为数据通信产品地通用操作系统平台，它以IP业务为核心，采用组件化地体系结构，在实现丰富功能特性地同时，提供基于应用的可裁剪和可拓展的功能。

### 2. 配置环境的搭建

- 1. 通过Console口配置

Console口(光电复用：只能用其中一个口，光口/电口)

<font color=#999AAA>使用SecureCRT，设备开机先做硬件质检</font>

<font color=#999AAA>建议：先写好脚本，再复制过去</font>

- 2. 通过Telnet方式配置

<font coor=#AAA>思科ISO平台，华为VRP平台，华三comeware平台</font>

### 3. eNSP使用

| 试图                                    | 说明                                                   |
| --------------------------------------- | ------------------------------------------------------ |
| 用户视图`<Huawei>`                      | 设备启动后缺省视图，可查看启动后基本运行状态和统计信息 |
| 系统视图`[Huawei]`                      | 配登系统全局通用参数的视图                             |
| 路由协议视图`[Huawei-ospf-1]`           | 配登路由协议参数的视图                                 |
| 接口视图`[Huawei-GigabitEthernet0/0/0]` | 配笪接口参数的视图                                     |
| 用户界面视图`[Huawei-ui-console0]`      | 配登登录设备的各个用户属性的视图                       |

修改路由器名字：

```shell
# todo 修改路由器名字
<Huawei>sys
Enter system view, return user view with Ctrl+Z.
[Huawei]sysname hw
[hw]
```

用户视图到系统视图：

```shell
# todo 1.用户视图到系统视图
<Huawei>system-view
[Huawei]quit  # 退出使用quit
```

系统模式到其他模式：

- 接口类型：
  - 通信接口，进通信接口：interface
  - 管理接口，进管理接口：user-interface console 0

```shell
# todo 2.系统模式到其他模式
[swith]interface Ethernet 1/0/24  # 由系统模式到接口模式
[swith]user-interface vty 0 4  # 由系统模式进去用户界面视图
[Huawei]user-interface aux 0  # 由系统视图模式到AUX口
[Huawei-ui-aux0]speed 115200  # 修改AUX口速率
[Huawei-Ethernet1/0/2Jreturn  # 直接退到用户视图模式
[R2]ospf
[R2-ospf-1]  # 系统视图进入路由协议视图
[swith]vlan 1
[swith-vlan1]
```

Huawei安全配置登录(使用线路下密码认证)：

```shell
# todo 3.Huawei安全配登(使用线路下密码认证)
[Huaweiluser-interface console 0  # 为console口设置密码
[Huawei-ui-console0]authentication-mode aaa  # 设登认证模式为本地账户
[Huawei-ui-console0]authentication- mode password  # 设翌认证模式为用户视图下密码 
[Huawei-ui-console0]set authentication password cipher huawei  # 设登用户视图下密码以秘文方式
```

使用本地用户及密码认证：

```shell
# todo 使用本地用户及密码认证
[DS-1]user-interface aux 0  # 为aux口设登密码
[Huawei-ui-console0]authentication-mode aaa  # 设登认证模式为本地账户
[Huawei-ui-console0]quit
[Huawei]aaa  # 进入aaa模式
[Huareiaaallocal-user admin password cipher huawei  # 创建用户admin并设登密码
[Huawei-aaa]local-user admin service-type terminal  # 允许验证终端登录服务
```

- **注**：
  - 拿到真实华为设备第一件事：创建/修改用户名和密码(一般有默认用户名密码)，不改默认密码**风险很大**！！！
  - 设置密码不要写空格！！！
  - 创建用户名、密码，然后给**权限**！！！至少给terminal权限
  - 配置写在内存里面，所以需要再保存

<font color=#999AAA>其他工具的使用：Wireshark、SecureCRT、FOFA、</font>
