---
layout: post
title: design a chat system
---


https://twitter.com/yanggege007/status/1627919590994092033

1. 如何解决亿级用户连接的可扩展性（连接server平滑可扩展），极端场景单房间百万级甚至千万级？
2. 消息协议的设计：增量同步协议，需满足不重不丢不乱序原则
3. 消息ID的唯一性& 自增（如微信的消息派号服务
4. 消息队列投递？读扩散OR 写扩散
5. 消息存储：万亿级历史消息的存储检索需求
6. 整个过程消息延迟的降低（加上公网传输延迟，需做到秒级内发送&投递）


感觉很多细节要补：
路由：单账号多设备群聊消息路由、广播和同步
连接：休眠、重连、pingpong 和 auth 机制
DB：消息读写扩散、可修改可撤回 & 隐私合规
ID 用snowflake+hash就可以，可用性角度来说做到不丢就行，客户端做去重（微信的消息是没有顺序保证的）

我之前在大厂的实践经验是，conn server前面还有一层httpdns层，负责多机房切流等工作，属于接入层的最前面。
另外，内部的容灾、切流、限流、熔断降级、全链路压测才是这套系统的挑战，要做到工程层面的高可用有很多课题
>连接分配服务就是httpdns 功能，我操盘过国内前三用户规模的IM架构落地哈

http tcp 前面 最好加个http dns角色 ，让不同的网络迅速找到适合的conn server  。conn server 前面有可能是下级 conn server 传递过来的消息 。很多服务并不只是tcp。http也占了一大部分。 消息走通不复杂 缺的是真的亿级用户的真实故障场景，没有故障场景 性能问题 这个架构进步不了，

巧了 我做过亿级用户的消息服务 你这构架只是个壳 里面要添加的东西太多了 用户管理 应对监管 然后数据结构设计是否是写扩散 连接能力输出设计等 里面的东西缺太多
>是的 确实只是个大概，大规模IM需要云很久的，很多都没画出来 例如消息审核之类

这个还是缺了很多东西。1. 比如如果你这个IM是服务于全球的。那个user shadding怎么做的，user roaming ，消息roaming怎么做？2. 支持不支持离线消息？ 离线保持多久？存储结构怎么做？如何保持一致性？最高性价比？数据丢失了怎么处理？3.支持不支持图片，视频，怎么做？IM系统 是非常复杂的系统



