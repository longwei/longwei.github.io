---
layout: post
title: AWS network
---

VPC: a gated parking buiding.
IGW: entrance in and out to internet
subnet: level in parking lot
route table: route signs
DNS resolver: parking lot staff can give direction when you are lost, or you want to go another subnet.

SG is for instance (personal lock): it make more sense to be stateful and deny everything. "only I can open the park lot door and don't need to check when I leave"
while the ACL is for the subnet (bigger scope), the security guard can't remember all the people in and out. so rule is stateless and default allow. "business as usual, but don't let in or out if a car is missing a license plate"


NAT gateway: ONE-WAY private instance can go outside
VPC endpoint: the elevator directly to the shopping mall.
VPC endpoint gateway: two of the shops(s3, dynamodb) are very popular, build another elevator

site to site VPN: virtual private road that still go through the internet, you can pay toll to AWS branch highway(global accelerator)
VPC peering: one way bridge that connect two parking lot and update the route table assuming parking spot number range (CIDR) don't overlap 
DX connect: dedicated private road, some lane public VIF goes to AWS endpoint, some lane private VIF goes to your VPC.
VGW: exit to on-prem, it could be either DX or VPN.
DX gateway: VGW plus, still not transitive (one way outbound) but that dedicated private road road can lead to more parking lots
Transite gateway: DGW plus, build not one way road but roundabount between parking lots.


VPC 及网络架构比喻：停车场模型
VPC：像一个有围墙的大型停车大楼，里面有不同层的停车区域（subnets）。
CIDR: 车位编号（CIDR）

Internet Gateway (IGW)：像停车大楼的出入口，让车可以自由进出互联网。

Subnet：停车楼的某一层，车辆可以停在不同层。

Route Table：停车场的指示牌，决定车辆如何在不同层和出口之间行驶。处理的是“路径”

DNS Resolver：停车场的咨询处/保安亭，可以告诉你去某个具体的地方怎么走，或者帮你找到另一层（subnet）。处理的是“地址查询”

安全机制
Security Group (SG)（针对实例）：

个人车锁，默认所有门都锁着，只有你允许的钥匙（规则）才能开锁。

状态保持（Stateful）——当你离开停车位时，系统不会再检查你出门的权限（出方向流量自动允许）。

“只有我能打开停车位的门，出去时也不用检查。”

Network ACL（NACL）（针对子网）：

子网的安保人员，负责检查进入和离开的车辆。

无状态（Stateless）——每次进出停车场都需要单独检查，无法记住谁刚刚进入过。

默认是允许所有车辆进出，但如果车牌不合规（特定规则），就会被拦下。

“平时车流畅通，但如果车牌不符合规则，就禁止进入或离开。”

不同的网络连接方式
NAT Gateway：单向旋转门，允许私有区域的车辆开出去，但外面的车辆无法进来。

VPC Endpoint：直通电梯，允许你直接到达 AWS 服务（API Gateway、Secrets Manager），而不用经过大门（IGW）。依赖 AWS PrivateLink，需要弹性网络接口（ENI）来建立连接

VPC Endpoint Gateway：专门通往 S3/DynamoDB 的高速电梯，因为这些店铺太受欢迎了，所以直接建了电梯通往它们。

跨VPC和外部网络连接
Site-to-Site VPN：虚拟私家路，但依然需要通过公网（互联网）。

你可以支付**高速公路通行费（AWS Global Accelerator）**来加快速度。

VPC Peering：一座连接两个停车场的桥，两边的管理处会更新路线图（Route Table），但车位编号（CIDR）不能重复。

Direct Connect (DX)：专用的私家道路：
比喻：你有一条专门的高速私家车道，不经过市区（互联网），直接连接你的停车大楼（VPC）和外部世界（On-Premises）。它提供比普通路（互联网）更稳定和快速的连接。

特点：通过 Direct Connect，你可以在企业数据中心和 AWS 之间建立专用连接，提供更低的延迟和更稳定的带宽。


Public VIF（公用车道）可直达 AWS 公共端点。

Private VIF（私有车道）直接通往你的 VPC，确保数据不经过公网。



VGW（Virtual Private Gateway）：出口通向你的公司（On-Prem），可以接到DX或VPN。
Virtual Private Gateway (VGW) - 专用停车场大门
比喻：你有一个专门的停车场大门，它连接你公司的私人停车场（On-Premises）和云上的停车场（VPC）。通过这扇大门，你可以安全地进出，不用经过外面的大路。

特点：VGW 连接 VPC 和你的本地网络（通过 VPN 或 Direct Connect），就像一扇安全的大门，允许流量进入或离开你的云环境。

DX Gateway（Direct Connect Gateway）：

高级版 VGW，可以连接多个 VPC，但仍然是单向的（不支持转发）。


Transit Gateway（TGW）：

交通枢纽，不像 DX Gateway 那样只能单向连接，而是能让多个停车场通过环形交叉路口相连，可以在多个 VPC 之间转发流量。
比喻：想象你在多个停车大楼（VPC）之间来回穿梭，但没有直接的连通通道。Transit Gateway 就像是一个巨大的多层立交桥，连接了多个停车大楼（VPC）。你可以在不同的大楼之间快速转移，但还是保持清晰的路标指引。

特点：通过 Transit Gateway，你可以方便地实现跨多个 VPC、VPN 和 AWS Direct Connect 的流量转发，类似于一个中心交通枢纽，使得多个停车大楼之间的流量可以无缝连接。