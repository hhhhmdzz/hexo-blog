---
title: ICT战队-第7次笔记
subtitle: 
summary: ACL访问控制列表 NAT网络地址转换
description: ACL访问控制列表 NAT网络地址转换
keyworks: 
author: hhhhmdzz
language: zh-Hans
timezone: Asia/Shanghai
date: 2022-06-29 10:50:10
categories: 
- ICT战队数据通信
tags: 
- HCIA
- 华为eNSP
mathjax: true
---

# ICT战队-第7次笔记

[toc]

## ACL

### **原理：ACL访问控制列表**

- **①原理：ACL概述**

<img src="./0283a4ab8ab44f58aa4d2582ee7e777d.png">

到了高阶段，ACL几乎都是用来**分类**用的。

<img src="./157ab06523e24c3384dbb9d1939daf5c.png">

ACL对进来的数据包过滤还是对出去的数据包进行过滤，ACL过滤方向只有一个，不能同时对同一个ACL设置进出两种流向。

写完ACL后，要指定是对进来的数据流过滤还是对出去的数据流过滤。

- **②原理：基于ACL的包过滤技术**
  <img src="./7f67dff6574f4d9c98a8635816d24b6f.png">

进路由器接口：无ACL，数据包直接进入路由器；有ACL，数据包根据ACL规则判断能否进入路由器
出路由器接口：先做路由转发，判断数据包该从哪个接口出去，再看该接口是否应用了ACL，无ACL直接出去，有ACL根据ACL规则判断能否出去。

**入站包过滤工作流程**：
<img src="./ac6edf53dd7b4e9d8cbd0bbffb78093b.png">

**出站包过滤工作流程**：
<img src="./24a0278454494c43922007104d5c6efa.png">

ACL按顺序匹配，不满足就一直往下匹配，一直匹配到最后一条匹配所有的规则
默认规则：(华为)允许。<font color=#999AAA>其他厂商默认拒绝所有，因此要设置相关的允许ACL规则。</font>

ACL规则建议：越详细越优先

<img src="./003b9e1751e44b36bf4b91ce10485304.png">

|  IPv4ACL类型  | ACL序号范围 | 区报文依据                                                   |
| :-----------: | :---------: | ------------------------------------------------------------ |
|   WLAN ACL    |  100 ~ 199  | 根据无线用户端的SSID制定匹配规则                             |
| 基本IPV4 ACL  | 2000 ~ 2999 | 只根据报文的源IP地址信息制定匹配规则                         |
| 高级IPv4 ACL  | 3000 ~ 3999 | 根据报文的源IP地址信息、目的IP地址信息、IP承载的协议类型、协议的特性等三、四层信息制定匹配规则 |
|    二层ACL    | 4000 ~ 4999 | 根据报文的源MAC地址、目的MAC地址、802.1Q优先级、二层协议类型等二层信息制定匹配规则 |
| 用户自定义ACL | 5000 ~ 5999 | 可以以报文的报文头、IP头等为基准，指定从第几个字节开始与掩码“与”操作，将从报文提取出来的字符串和用户定义的字符串进行比较，找到匹配的报文。 |

基本ACL：基本访问控制列表只根据报文的源IP地址信息制定规则——即只能过滤<font color="red">源IP</font>
高级ACL：高级访问控制列表根据报文的源IP地址、目的IP地址、IP承载的协议类型、协议特性等三、四层制定规则——即过滤<font color="red">源IP、目的IP、协议、源端口、目标端口</font>

建议(不是硬性规定)：
基本ACL：离目标越近越好。如果ACL设置在源出口，那么源设备发送的数据包都不能发送到其他地方，而被拦截在源出口，因为源出口设置了ACL。
高级ACL：离源越近越好

ACL配置命令：
**1.** ACL命名

```shell
# show ACL命名
[R]acl name dacongming 2000
```

**2.** ACL配置命令

```shell
# show 命令，随便看看
[AR1]acl 2000
[AR1-acl-basic-2000]rule permit ?
  fragment             Check fragment packet
  none-first-fragment  Check the subsequence fragment packet  
  source               Specify source address
  time-range           Specify a special time
  vpn-instance         Specify a VPN-Instance
  <cr>                 Please press ENTER to execute command 
[AR1-acl-basic-2000]rule permit source 1.1.1.1 ?
  IP_ADDR<X.X.X.X>  Wildcard of source
  0                 Wildcard bits : 0.0.0.0 ( a host )
[AR1-acl-basic-2000]rule permit source 1.1.1.1 0 ?
  fragment             Check fragment packet
  none-first-fragment  Check the subsequence fragment packet  
  time-range           Specify a special time
  vpn-instance         Specify a VPN-Instance
  <cr>                 Please press ENTER to execute command 
[AR1-acl-basic-2000]rule permit source 1.1.1.1 0 
```

### **实验一：基本ACL**

ACL包过滤配置：
<img src="./ab881086deaf4d84b6caecb1070bb810.png">

- **STEP1：** 网络拓扑

<img src="./28819471e4684da2a05cbdf49f9359f1.png">

- **STEP2.1：** 配置DHCP

```shell
# todo AR2：配置DCHP接口
[AR2-i]int g0/0/1
[AR2-GigabitEthernet0/0/1]dhcp select interface
```

```shell
# todo AR1：配置DHCP地址池
[AR1]dhcp enable 
Info: The operation may take a few seconds. Please wait for a moment.done.
[AR1]ip pool 
Info: It's successful to create an IP address pool.
[AR1-ip-pool-1]network 192.168.1.0 mask 24
[AR1-ip-pool-1]gateway-list 192.168.1.1
[AR1-ip-pool-1]int g0/0/0
[AR1-GigabitEthernet0/0/0]dhcp select interface
[AR1-GigabitEthernet0/0/0]int g0/0/1
[AR1-GigabitEthernet0/0/1]dhcp select global
```

- **STEP2.2：** 配置路由协议

```shell
# todo AR1：配置OSPF
[AR1]ospf
[AR1-ospf-1]a 0
[AR1-ospf-1-area-0.0.0.0]network 12.1.1.0 0.0.0.255
[AR1-ospf-1-area-0.0.0.0]a 1  # 创建area1
[AR1-ospf-1-area-0.0.0.1]a 2  # 创建area2
[AR1-ospf-1-area-0.0.0.2]int g0/0/0
[AR1-GigabitEthernet0/0/0]ospf enable 1 area 1
[AR1-GigabitEthernet0/0/0]int g0/0/1
[AR1-GigabitEthernet0/0/1]ospf enable 1 area 2

# todo AR2：配置OSPF
[AR2]ospf
[AR2-ospf-1]a 0
[AR2-ospf-1-area-0.0.0.0]network 12.1.1.0 0.0.0.255
[AR2-ospf-1-area-0.0.0.0]a 3
[AR2-ospf-1-area-0.0.0.3]network 10.1.2.0 0.0.0.255
```

- **STEP2.3：** 查看路由表(略：有所有区域的路由条目)
- **STEP2.4：** 测试网连通性(略：全网互通)

- **STEP3.1：** 配置ACL

```shell
# todo AR1：配置ACL
[AR1]acl 2000
[AR1-acl-basic-2000]rule 5 deny source 10.1.2.254 0.0.0.0
[AR1-acl-basic-2000]dis th
[V200R003C00]
#
acl number 2000  
 rule 5 deny source 10.1.2.254 0 
#
return
[AR1-acl-basic-2000] 
[AR1-acl-basic-2000]int g0/0/0 
[AR1-GigabitEthernet0/0/0]traffic-filter ?
  inbound   Apply ACL to the inbound direction of the interface 
  outbound  Apply ACL to the outbound direction of the interface 
[AR1-GigabitEthernet0/0/0]traffic-filter outbound  acl 2000
```

注：
1.ACL序列号步长为5，越小越优先
2.尽量不要删除生产环境中的配置
3.0表示匹配，1表示忽略。
4.从PC1到PC2，从AR1的g0/0/0接口出去，所以在g0/0/0配置ACL

- **STEP3.2：** 测试连通性

PC1和PC2主间无法通信

- **STEP0：** ACL默认规则

各个厂商ACL默认规则不一样，我们可以不考虑ACL默认规则，自己增加，例如：

```shell
# todo 在配置完所有规则后，最后配置放行/拒绝所有规则
[AR1]acl 2000
[AR1-acl-basic-2000]rule permit source 1.1.1.1 255.255.255.255  # 放行所有写法1
[AR1-acl-basic-2000]rule permit source any  # 放行所有写法2
```

- **STEP4.1：** 接口取消应用ACL

```shell
# todo AR1：接口取消应用ACL
[AR1-GigabitEthernet0/0/0]un traffic-filter outbound  # 接口取消应用ACL
```

- **STEP4.2：** 测试连通性

主机PC1和PC2能ping通。

### **实验二：扩展ACL**

- **STEP5.1：** 修改网络拓扑

在上个实验网络配置不变的情况下，添加设备。

<img src="./efac04187f8147cc8662f6a7ab13de67.png">

- **STEP5.2：** 客户、服务区配置

基础配置：

| 设备    | 本机地址      | 子网掩码      | 网关        |
| ------- | ------------- | ------------- | ----------- |
| Client1 | 10.1.2.250    | 255.255.255.0 | 10.1.2.2    |
| Server1 | 192.168.1.250 | 255.255.255.0 | 192.168.1.1 |

服务器信息：
-> HttpServer：文件根目录：选择一个有.html文件的目录
-> 启动

**扩展ACL要求**:
**1.** PC2不能和PC2通信
**2.** PC1可以和其他所有主机通信
**3.** 仅允许10.1.2.0/24中的Client1通过Server1
**4.** 其他通信都被允许

- **STEP6.1：** 配置扩展ACL

```shell
# todo AR2：扩展ACL
[AR2]acl 3000
[AR2-acl-adv-3000]rule deny ip source 10.1.2.254 0 destination 10.1.1.254 0
[AR2-acl-adv-3000]rule permit tcp source 10.1.2.250 0 destination 192.168.1.250 0 destination-port eq 80
[AR2-acl-adv-3000]rule deny ip source any destination 192.168.1.250 0
[AR2-acl-adv-3000]rule permit ip
```

- **STEP6.2：** 查看扩展ACL信息

```shell
# todo AR2：查看ACL信息
[AR2-acl-adv-3000]dis acl 3000  # 查看一个ACL
Advanced ACL 3000, 4 rules
Acl's step is 5
 rule 5 deny ip source 10.1.2.254 0 destination 10.1.1.254 0 
 rule 10 permit tcp source 10.1.2.250 0 destination 192.168.1.250 0 destination-port eq www 
 rule 15 deny ip destination 192.168.1.250 0 
 rule 20 permit ip 

[AR2-acl-adv-3000]dis acl all  # 查看所有ACL
 Total quantity of nonempty ACL number is 1 

Advanced ACL 3000, 4 rules
Acl's step is 5
 rule 5 deny ip source 10.1.2.254 0 destination 10.1.1.254 0 
 rule 10 permit tcp source 10.1.2.250 0 destination 192.168.1.250 0 destination-port eq www 
 rule 15 deny ip destination 192.168.1.250 0 
 rule 20 permit ip 
```

- **STEP6.3：** 进入接口调用ALC

```shell
# tood AR2：进入接口调用ACL
[AR2-acl-adv-3000]int g0/0/1
[AR2-GigabitEthernet0/0/1]traffic-filter inbound acl 3000
```

注：
1.配置inbound还是outbound要看ACL的目的和源地址
2.HTTP端口80，HTTPS端口43，自己1024-62235随机端口

- **STEP6.4：** 测试连通性

**PC1：**
可以ping通：PC3(192.168.1.254)
不可以ping通：PC2(10.1.1.254)、Server1(192.168.1.250)

**Client1：**
-> 地址：`http://192.168.1.250/[Server1中的某个.html`
-> 获取
-> 可以看到以下内容

```shell
HTTP/1.1 200 OK
Server: ENSP HttpServer
Auth: HUAWEI
Cache-Control: private
Content-Type: text/html
Content-Length: 34155
```

可以看到服务器的响应状态码为**200**，即成功接收。

- **STEP7.1：** 修改网络拓扑

<img src="./892b1040ebcf4760a31582c1dc618898.png">

- **STEP7.2：** Client2配置

基础配置：

| 设备    | 本机地址   | 子网掩码      | 网关     |
| ------- | ---------- | ------------- | -------- |
| Client2 | 10.1.2.249 | 255.255.255.0 | 10.1.2.2 |

- **STEP7.3：** 测试连通性

尝试使用Client2登录Server1

**Client2：**
-> 地址：`http://192.168.1.250/[Server1中的某个.html`
-> 获取
-> 可以看到以下内容

```shell
Connect server failure.
```

连接服务器失败

## NAT

### **原理：NAT网络地址转换**

<img src="./f15f5e05d92d4e9381cf5d5e6ddcbbd7.png">

**NAT实验均使用以下网络拓扑，直接打开老师的网络拓扑即可**:

<img src="./318d301e707c40e7939e8f734bd74d62.png">

### **实验三：动态地址NAT(Pooled NAT)**

- **STEP1：** 网络拓扑(略)

- **STEP2：** 配置ACL

使用ACL分类：哪些地址需要做NAT，哪些地址不需要做

```shell
[R1-acl-basic-2000]rule deny source 10.1.1.0 0.0.0.255
[R1-acl-basic-2000]rule permit source any
```

- **STEP3：** 配置公网地址池范围

```shell
[R1]nat address-group 0 192.168.1.66 192.168.1.67
```

- **STEP4：** 出接口配置NAT

```shell
[R1-GigabitEthernet0/0/2]nat outbound 2000 address-group 0
```

NAT：都在接口调用
PAT：基于端口port的NAT转换
no-pat：不基于端口port的NAT转换
<font color=#999AAA>eNSP对NAT兼容性比较差</font>

### **实验四：网络地址端口转换NAPT(Port-Level NAT)**

- **STEP1：** 网络拓扑(略)

- **STEP2：** 配置NAPT

```shell
[R2]acl 2000
[R2-acl-basic-2000]rule permit
[R2-acl-basic-2000]inter g0/0/0
[R2-Gig！@abitEthernet0/0/0]nat outbound 200
```

- **STEP3：** 测试网络连通性

PC1 可以 ping 通 R1 接口 g0/0/2 的 IP

### **实验五：端口映射**

端口映射技术：其实是静态 NAT 与 PAT 的结合，即局域网中主机的某一端口通过静态方式映射到公网 IP 地址端口上。映射技术只能单一的实现所映射端口的特定功能，但一台主机可映射多个不同端口到同一公网 IP 端口上。如 23 代表 telnet，80 代表 http，21 代表 ftp 等知名端口。

- **STEP1：** 网络拓扑(略)

- **STEP2：** 配置NAT端口映射

```shell
[R1-GigabitEthernet0/0/2]nat server protocol tcp global current-interface 8003 inside 10.1.3.250 80
```

- **STEP3：** 测试连通性

Client1可以通过http访问Server1

### **实验六：静态NAT(Static NAT)**

- **STEP1：** 网络拓扑(略)

- **STEP2：** 配置静态NAT

```shell
[R1-GigabitEthernet0/0/2]nat static global 192.168.1.68 inside 10.1.1.254
```

- **STEP3：** 测试连通性

PC1：可以ping通192.168.1.68
PC2：抓包可以看到ICMP数据包

### 笔记

后面没声，不知道在干嘛，需要可以去看上一届的视频和笔记：[数通2021-01-25](D:/STDATA/VSCodeData/MarkDown/ICT数通19/2021-01-25/2021-01-25.md)

- 老师笔记

```shell
动态NAT
步骤一：使用分类哪些主机进行NAT转换
[R1]acl 2000
[R1-acl-basic-2000]rule deny source 10.1.1.0 0.0.0.255
[R1-acl-basic-2000]rule permit source any
[R1-acl-basic-2000]q
步骤二：定义公网地址池范围
[R1]nat add 0 192.168.1.66 192.168.1.67
步骤三：出接口配置NAT
[R1]int g0/0/2
[R1-GigabitEthernet0/0/2]nat outbound  2000 address-group 0

NART：端口NAT
[R2]acl 2000
[R2-acl-basic-2000]rule permit 
[R2-acl-basic-2000]q
[R2]int g0/0/0
[R2-GigabitEthernet0/0/0]nat outbound 2000
测试方法：PC1可以ping通AR1，g0/0/2接口IP

NAT server(接口映射)
[R1-GigabitEthernet0/0/2]nat server protocol tcp global current-interface 8003 inside 10.1.3.250 80
测试方法：client1可以通过http访问server1

静态NAT转换
[R1-GigabitEthernet0/0/2]nat static global 192.168.1.68 inside 10.1.1.254
测试方法：PC1可以ping通192.168.1.68，同时在PC2、5上抓包可以看到ICMP数据包
```

