---
title: 一日一练-网络 WireShark 抓包分析
date: 2021-10-21 09:04:58
tags:
  - 技术
  - 一日一练
  - 网络
  - 转载
categories: 技术
---


本文转载自[CSDN WireShark抓包分析](https://blog.csdn.net/hebbely/article/details/54424823)，有部分删节，看原文请到原地址。

本文介绍了抓包数据含义，有 TCP 报文、Http 报文、DNS 报文。

<!--more-->

## 一、TCP报文
TCP 是`面向连接`的通信协议，通过`三次握手`建立连接，通讯完成时要拆除连接，由于 TCP 是面向连接的所以只能用于`点对点`的通讯。

源 IP 地址：发送包的 IP 地址；
目的 IP 地址：接收包的 IP 地址；
源端口：源系统上的连接的端口；
目的端口：目的系统上的连接的端口。  

TCP 是因特网中的传输层协议，使用三次握手协议建立连接。当主动方发出 `SYN` 连接请求后，等待对方回答 `SYN，ACK`。这种建立连接的方法可以防止产生错误的连接，TCP 使用的`流量控制协议`是可变大小的滑动窗口协议。

第一次握手：建立连接时，客户端发送 `SYN` 包(SEQ=x)到服务器，并进入 `SYN_SEND` 状态，等待服务器确认。
第二次握手：服务器收到 `SYN` 包，必须确认客户的 `SYN`(ACK=x+1), 同时自己也送一个 `SYN` 包(SEQ=y),即 `SYN+ACK` 包，此时服务器进入 `SYN_RECV` 状态。
第三次握手：客户端收到服务器的 `SYN+ACK` 包，向服务器发送确认包 `ACK`(ACK=y+1), 此包发送完毕，客户端和服务器进入 `Established` 状态，完成三次握手。

### 1.封包详细信息
{% asset_img 1.png %}

A. 第一行，`帧` Frame 36838 指的是要发送的数据块，其中，所抓帧的序号为 36838，捕获字节数等于传送字节数：70 字节；

B. 第二行，`以太网`，`有线局域网技术`，是`数据链路层`。源 Mac 地址为88:5d:90:00:00:25；目标 Mac 地址为 00:25:22:b5:b9:92；

C. 第三行，`IPV4 协议`，也称网际协议，是`网络层`；源 IP 地址为 192.168.21.175；目标 IP 地址为 192.168.21.156；

D. 第四行，`TCP 协议`，也称`传输控制协议`，是`传输层`；源端口(10086)；目标端口(50132)；序列号(1361)；ACK 是 TCP 数据包首部中的确认标志，对已接收到的 TCP 报文进行确认，值为1 表示确认号有效；长度为 16；

E. 第五行，`数据`共有 16 字节

### 2.Frame 信息分析
{% asset_img 2.png %}

A. Arrival Time：到达时间，值为Jan 14, 2017 08:52:56.239204000

B. EPoch Time：信息出现时间，值为1484355176.239204000秒

C. [ Time delta from previous captured frame: 0.001472000 seconds] ：与之前捕获的数据帧时间差：0.001472000秒；

    [Time delta from previous displayed frame: 0.001472000 seconds]：与之前显示的帧时间差： 0.001472000秒；

    [Time since reference or first frame: 1278.276505000 seconds]：距参考帧或第一帧的时间差：1278.276505000秒；

D. Frame Number: 36838，帧编号为36838；

E. Frame Length: 70 bytes (560 bits)，帧长度为70字节；

    Capture Length: 70 bytes (560 bits)，捕获到的长度为70字节；
F. [Frame is marked: False]，帧标记：无；

    [Frame is ignored: False]，帧被忽略：无；

G. [Protocols in frame: eth:ip:tcp:data]，协议帧：eth(以太网)、IP、tcp、data

H. [Coloring Rule Name: TCP]，色彩规则名称：TCP；

    [Coloring Rule String: tcp]，色彩规则字符串：TCP；

### 3.EthernetⅡ信息分析
{% asset_img 3.png %}

A. Destination: AsrockIn_b5:b9:92 (00:25:22:b5:b9:92)，目标Mac地址为00:25:22:b5:b9:92

B. Source: 88:5d:90:00:00:25 (88:5d:90:00:00:25)，源Mac地址为88:5d:90:00:00:25

C. Type: IP (0x0800)，类型是 IP 数据包

### 4.IPv4 协议信息分析
{% asset_img 4.png %}

A. Version: 4，IP 协议版本为 IPv4；

    Header length: 20 bytes，头部数据长度为 20 字节；

B. Differentiated Services Field: 0x00 (DSCP 0x00: Default; ECN: 0x00: Not-ECT (Not ECN-Capable Transport))，区分的服务领域：0x00 (默认的是 DSCP：0x00)；

C. Flags: 0x02 (Don't Fragment)，不支持分组；

    Fragment offset: 0，分组偏移量为 0； 

D. Time to live: 64，TTL，生存时间为 64，TTL 通常表示包在被丢弃前最多能经过的路由器个数，当数据包传输到一个路由器之后，TTL 就自动减 1，如果减到 0 了还没有传送到目标主机，那么就自动丢失。

E. Header checksum: 0xcebd [correct]，头部校验和

F. Source: 192.168.21.175 (192.168.21.175)，源IP地址为192.168.21.175；

    Destination: 192.168.21.156 (192.168.21.156)，目标IP地址为192.168.21.156；

### 5.Trasmission Control Protocol 信息分析

其中，对应的TCP首部的数据信息
{% asset_img 5.png %}

A. 端口号，数据传输的 16 位源端口号和 16 位目标端口号(用于寻找发端和收端应用进程)；

B. 相对序列号，该数据包的相对序列号为 1361 (此序列号用来确定传送数据的正确位置，且序列号用来侦测丢失的包)；下一个数据包的序列号是 1377；

C. Acknowledgment number 是 32 位确认序列号，值等于 1 表示数据包收到，确认有效；

D. 手动的数据包的头字节长度是 20 字节；

E. Flags，含 6 种标志；ACK：确认序号有效；SYN：同步序号用来发起一个连接；FIN：发端完成发送任务；RST：重新连接；PSH：接收方应该尽快将这个报文段交给应用层；URG：紧急指针(urgentpointer)有效；

F. window，TCP 的流量控制由连接的每一端通过声明的窗口大小来提供。窗口大小为字节数，起始于确认序号字段指明的值，这个值是接收端正期望接收的字节。窗口大小是一个 16bit 字段，因而窗口大小最大为 65536 字节，上面显示窗口大小为 1825 字节；

G. Checksum，16 位校验和，检验和覆盖了整个的 TCP 报文段，由发端计算和存储，并由收端进行验证；

### 6.Data 信息分析
{% asset_img 6.png %}

A.  TCP 报文段中的数据(该部分是可选的)，长度为16字节；

## 二、Http 报文
链路层的信息上是以帧的形式进行传输的，帧封装了应用层、传输层、网络层的数据。而 Wireshark 抓到的就是链路层的一帧；

### 1. 封装包详细信息
{% asset_img 7.png %}

A. 第一行，`帧 Frame` 12411 指的是要发送的数据块，其中，所抓帧的序号为 12411，捕获字节数等于传送字节数：233 字节；

B. 第二行，`以太网`，`有线局域网技术`，是`数据链路层`。源 Mac 地址为 24:69:68:6b:78:96；目标 Mac 地址为 00:25:22:b5:b9:92；

C. 第三行，`IPV4 协议`，也称`网际协议`，是`网络层`；源 IP 地址为 220.181.57.234；目标 IP 地址为 192.168.21.156；

D. 第四行，`TCP 协议`，也称`传输控制协议`，是`传输层`；源端口(80)；目标端口(53985)；序列号(1)；ACK 是 TCP 数据包首部中的确认标志，对已接收到的 TCP 报文进行确认，值为 1 表示确认号有效；长度为 179；

E. 第五行，`Http 协议`，也称`超文本传输协议`，是`应用层`。

### 2. Http 请求报文分析
{% asset_img 8.png %}

报文分析：
{% asset_img 9.png %}

在抓包分析过程中还发现了另外一些 http 请求报文中所特有的首部字段名，比如下面 http 请求报文中橙黄色首部字段名：
{% asset_img 10.png %}

### 3. Http 响应报文分析
{% asset_img 11.png %}

报文分析：
{% asset_img 12.png %}

## 三、DNS 报文

### 1. 封包详细信息
{% asset_img 13.png %}

A. 第一行，`帧 Frame` 12237 指的是要发送的数据块，其中，所抓帧的序号为 12237，捕获字节数等于传送字节数：133 字节；

B. 第二行，`以太网`，`有线局域网技术`，是`数据链路层`。源 Mac 地址为 24:69:68:6b:78:96；目标 Mac 地址为 00:25:22:b5:b9:92；

C. 第三行，`IPV4 协议`，也称`网际协议`，是`网络层`；源 IP 地址为 192.168.211.254；目标 IP 地址为 192.168.211.84；

D. 第四行，`UDP 协议`，是`传输层`；源端口 domain(53)；目标端口(65219)；

E. 第五行，`DNS 协议`，是`应用层`。


### 3. DNS 查询报文
{% asset_img 14.png %}

报文分析：
{% asset_img 15.png %}

### 4. DNS 响应报文
{% asset_img 16.png %}

报文分析：
{% asset_img 17.png %}

## 四、Ping
{% asset_img 18.png %}










