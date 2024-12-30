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

