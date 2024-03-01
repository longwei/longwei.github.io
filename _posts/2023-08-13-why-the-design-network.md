---
layout: post
title: why the design network
---


**newwork**
1. why 3 handshake

what's connection? socket + window size + sequence number
三次握手的主要目的是避免历史错误连接的建立并让通信的双方确定初始序列号
The principle reason for the three-way handshake is to prevent old duplicate connection initiations from causing confusion.

如果 TCP 建立连接只能通信两次，那么接收方只能选择接受或者拒绝发送方发起的请求，它并不清楚这一次请求是不是由于网络拥堵而早早过期的连接
TCP 选择使用三次握手来建立连接并在连接引入了 RST 这一控制消息

『四次握手』：TCP 协议的设计可以让我们同时传递 ACK 和 SYN 两个控制信息，减少了通信次数，所以不需要使用更多的通信次数传输相同的信息

2. why DNS use UDP?
从理论上来说，一个 UDP 数据包的大小最多可以达到 64KB，这对于一个常见的 DNS 查询其实是一个非常大的数值；但是在实际生产中，一旦数据包中的数据超过了传送链路的最大传输单元（MTU，也就是单个数据包大小的上限，一般为 1500 字节），当前数据包就可能会被分片传输、丢弃，部分的网络设备甚至会直接拒绝处理包含 EDNS(0) 选项的请求，这就会导致使用 UDP 协议的 DNS 不稳定。
无论是选择 UDP 还是 TCP，最核心的矛盾就在于需要传输的数据包大小


3. TCP 协议为什么在弱网环境下有严重的性能问题

本文将分析在弱网环境下（丢包率高）影响 TCP 性能的三个原因：

TCP 的拥塞控制算法会在丢包时主动降低吞吐量；
TCP 的三次握手增加了数据传输的延迟和额外开销；
TCP 的累计应答机制导致了数据段的传输；

早期互联网的大多数计算设备都通过有线网络连接，出现网络不稳定的可能性也比较低，所以 TCP 协议的设计者认为丢包意味着网络出现拥塞，一旦发生丢包，客户端疯狂重试就可能导致互联网的拥塞崩溃，所以发明了拥塞控制算法来解决该问题。

但是由于 TCP ACK 的语义是当前数据段前的全部数据段都已经被接收和处理，所以接收方无法发送 ACK 消息，由于发送方没有收到 ACK，所有数据段对应的计时器就会超时并重新传输数据。在丢包较为严重的网络下，这种重传机制会造成大量的带宽浪费

either rebuild on top of UDP, QUIC or 优化Selective ACK, TCP Fast Open

4. UDP 头只有 8 个字节就够了？

source port, target port, length, check_sum
我们可以将应用到应用之间的传输过程分成两个部分：主机到主机的数据传输和主机到应用的数据转发
* UDP 协议底层的网际协议（Internet Protocol，IP）会负责数据包在主机之间的传输；
* UDP 协议首部的端口号用于定位处理数据的具体进程并转发数据；

(Application) http/ftp
(Transport) TCP 和 UDP 等传输层协议的主要作用是为应用建立基本的数据管道，为特定任务提供数据传输的功能
(Network) IP 等网络层协议的主要作用是寻址和路由，它能够帮助我们将数据发送目标的主机。

port: uniquely identify a connection endpoint or specific service(process)
process ID is internal identifier. 同时对外提供多个服务

长度和校验码可以省略，长度可以从 IP 的长度推算出来，校验码没有就没有呗，RFC 里也说不想算的话可以填0
其他传输层协议有的有端口号的概念有的没有，我感觉主要是看是否需要在一个主机上跑多个使用这个协议的程序，像 ICMP 这种没必要跑多个就没有端口的概念，类似 TCP、UDP 这种需要跑多个的就需要端口号来区分。

5. 为什么 TCP/IP 协议会拆分数据

find the MTU:
ICMP 是互联网控制消息协议（Internet Control Message Protocol，ICMP），它能在 IP 主机之间传递控制消息

IP: UDP: Payload 1
IP: Payload 2

vs target state

IP: TCP: Payload 1
IP: TCP: Payload 2

IP 协议的分片机制都会导致哪些问题？

TCP 连接双方是如何确定连接的 MSS？这个值是动态的么？
* 最小 MTU 变了会再走一次 Path MTU Discovery


6. 为什么流媒体直播的延迟很高

主播端：音视频采集、音视频编码encode、推流；
流媒体服务器：直播流收集、音视频转码transcode、直播流分发；
观众端：拉流、音视频解码decode、音视频播放

音视频使用的编码格式决定了客户端只能从特定帧开始解码；
音视频传输使用的网络协议切片大小决定了客户端接收数据的间隔；
服务器和客户端为了保证用户体验和直播质量预留缓存；

***数据编码***
H.264 使用关键帧（Intra-coded picture，I 帧）作为视频的全量数据，
不断使用向前参考帧（Predicted picture，P 帧）和
双向参考帧（Bidirectional predicted picture，B 帧）对全量的数据进行增量式的修改以达到压缩的目的。

客户端在找到第一个可以播放的关键帧的时间Group of pictures

***数据传输***
最常见的两种网络协议是实时消息传输协议（Real Time Messaging Protocol，RTMP）和 HTTP 实时流式传输协议（HTTP Live Streaming，HLS）
RTMP 以及 HTTP-FLV 等基于流分发的协议切片粒度很小，延迟在 3s 以下，可以看做实时的传输协议；
而 HLS 协议是基于文件分发的协议，它的切片粒度很大，在实际使用中可能会带来 20 ~ 30s 的延迟。


***多端缓存***
每个节点都cache streaming data


7. 为什么 TCP 协议有粘包问题
nagle buffer + streaming
TCP 协议是面向字节流的协议，它可能会组合或者拆分应用层协议的数据；
应用层协议的没有定义消息的边界导致数据的接收方无法拼接数据；

8. 为什么 TCP 协议有 TIME_WAIT 状态
TIME_WAIT 仅在主动断开连接的一方出现，被动断开连接的一方会直接进入 CLOSED 状态，进入 TIME_WAIT 的客户端需要等待 2 MSL 才可以真正关闭连接。TCP 协议需要 TIME_WAIT 状态的原因和客户端需要等待两个 MSL 不能直接进入 CLOSED 状态的原因是一样的

* 当防止延迟的数据段被其他使用相同源地址、源端口、目的地址以及目的端口的 TCP 连接收到: 使用相同端口号的 TCP 连接被重用后, 过期的消息却可能被客户端正常接收
* 保证 TCP 连接的远程被正确关闭，即等待被动关闭连接的一方收到 FIN 对应的 ACK 消息

9. 为什么 MAC 地址不需要全球唯一
保证 MAC 地址在局域网中唯一就不会造成网络问题，不同局域网中的 MAC 地址可以相同


10. 为什么集群需要 Overlay 网络
云计算中集群内的、跨集群的或者数据中心间的虚拟机和实例的迁移比较常见；
单个集群中的虚拟机规模可能非常大，大量的 MAC 地址和 ARP 请求会为网络设备带来巨大的压力；
传统的网络隔离技术 VLAN 只能建立 4096 个虚拟网络，公有云以及大规模的虚拟化集群需要更多的虚拟网络才能满足网络隔离的需求






