---
layout: post
title: relfection on virtualization background
---


# OSTEP

虚拟化CPU： 类似'context' switch. 只是现在save the entire machine state of one OS。
新的难点在于it could be in different privilege level.
如果是user mode好说，涉及到kernel mode有点麻烦,

幸运的是VMM不需要知道如何处理中断，只需要知道真的trap handler在哪里.
类比，只传球，不会射门。

但是what mode should the OS run in? user mode权限不够， kernel mode又没有保护。　Disco利用了MIPS的特殊mode(supervisor mode with reserved memory)
不然只能OS在user mode自己维护page table和tlbs, 然后switch to OS的时候传给hosted OS来update。

**我的问题：硬件的context switch真的是可以随时随地恢复的么？比如pipeline什么的？same instruction在各个ring行为一致么？**


Memory难点：MMU: virtual address(VPN) -> physical address(PFN). 现在是virtual address (VM)-> virtual address (MMU)-> physical address。
现在还得自己维护shadow page table, 一旦cache了，就有consistency的问题。
如何保持和OS的sync？不能每次发现space change就flush shadow page table和TLB吧。

solution:自己维护TLB： the designers of Disco added a VMM-level “software TLB，" direct translation address is in TLB and MMU.

Schedule：中间又隔了一层，没有传统的反馈，OS如何有效分配时间片？类比，在团队隔离很好的公司，如何知道隔壁组在摸鱼？
Disco: 也没有好办法只有额外pass implicit information (context hint)

IO: skip/OS “hosted” configuration. hosted OS也有专门的host模式。


# Formal Requirements for Virtualizable Third Generation Architectures
太多公式和证明，看不太下去。学术上是定义了问题边界，
## 什么是virtualization, 有什么特性。。
但是又skip了具体的I/O和interruption。

**问题: 为什么又skip IO？这块难点在哪？类比，高铁要快，站点就不能多。频繁switch的话，throughput上不去，如何一边开飞车一边上下乘客？**

从外界看：equivalence， efficiency， resource control
从内部看一台机器状态：executable storage + processor mode + program counter + relocation-bounds register R


## 什么是VMM
虚拟化需要一个control program就叫VMM吧，其功能有 = disptcher + allocator + interpreters
disptcher(针对instruction和trap/processor mode)
allocator 针对resource control(executable storage)
interpreters针对CPU(PC+register)

如何等价？virtual machine monitor map讲了两个世界之前的instruction set如何mapping

### 然后基于以上的定义各种证明。。。



# Disco: Running Commodity Operating Systems on Scalable Multiprocessors
## Overview:
要想提高效率，就的用commodity OS + hardware, 需要在这个基础上提供这个unified抽象层。
针对每个硬件从新造轮子是死路。是可以有统一模型的，使得多个resource看起来是一个。

Pro:
利用很小的overhead就能share memory and disk between virtual machines. 不仅可以做到一台机器的抽象，这个抽象可以扩展到多个机器/OS
global buffer cache (CDN?)。

Con:
1. overhead cost:
 a. privileged instructions + TLB instructions must be trapped and emulated
 b. OS code & cache replication
2. Resource management: lack of information to make a good decision
3. communication and sharing:
 a. how to communicate between VM?
 b. how to virtualize resources that are designed to use exclusively? Disk？


## 如何virtual CPU？
direct exection而不emulation.
大部分都是直接跑，难在privilege /tlb instruction.这部分必须trap and emulation
而为了trap and emulation，则相应的信息都存在各个虚拟机上。
类比看OS是怎么用process table来context swtich的



## 如何virtual memory：

key idea: 多了一层抽象，就要多一层mapping。如何降低overhead？flat mapping

### TLB
before os: TLB: app virtual address -> physical address,
now disco impl software TLB: app virtual address -> machine address + physical address->machine address，

** 我的问题： How VMM maintain software TLB while not the host OS? disco怎么检测hw tlb有变动而且能保持两个tlb一致？**

### zero paged
new page是一定要zeroed的，分享状态避免double work.


处理多机NUMA：
## key idea
 有点让我想起了google file system，分割数据的逻辑位置和物理位置。

1. data affinity: 经常被一个node用，那么这个node应该own这段data => migration. 利用flash自带的cache-miss-counting来决定migration/placement
2. Transparent Page replication: single master for write and read replicates among all nodes,
   * 问题1. 怎么解决inconsistenct问题, 类似distributed cache
   * 问题2: 怎么解决concurrent/lock contention问题， 类似distributed transaction


















