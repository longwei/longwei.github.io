---
layout: post
title: Para-Virtualization Reflection
---

TODO: Pre-Meetup Questions from CSE 291

Q: Why can Xen allow guest OS system call handlers to be accessed directly (without any ring-0 Xen involvement) but not guest page fault handler?

A: 

For system calls in a para-virtualized environment, the guest OS is modified to be aware of the hypervisor, which trusts the guest OS to manage system calls directly. (No context switching as system call has explicit and predictable behavior)


Page Fault: when a program accesses a logic memory that is not in physical memory. 

Swap and memory mapping are needed in this case.

We can't allow an application to access and modify any memory as it wishes. So, the hypervisor must intercept the page faults to enforce isolation. Memory management is critical and must be strictly controlled and validated by the hypervisor due to isolation/security.



Q: What's the benefit of using asynchronous event notifications from Xen to a VM?

A: "Data is transferred using asynchronous I/O rings. An event mechanism replaces hardware interrupts for notifications."

"These callbacks can be ‘held off’ at the discretion of the guest OS — to avoid extra costs incurred by frequent wake-up notifications."


* Avoid priority inversion or interrupt inside interrupt

* Performance/Resource by giving the domain the option to defer/batch the event handling

* Uniform async message interface vs special IRQ

* Lower overhead




Q: What goals of Xen are not valid or less valid in today's cloud environments?

A: 

1. from down: hardware-assisted(KVM) can better handle system call trap and MMU virtualization

2. from up: The shift towards Docker and k8s has reduced the emphasis on traditional VMs.

3. From time: We need to maintain the modified OS, which is quite a complex overhead.



[Optional] you can also list the questions you have in the paper. In the meetup, I will go through the questions in FIFO order. 


Q1: To our knowledge, rings 1 and 2 have not been used by any well-known x86 OS since OS/2, 十几年没人用, intel还留着。backward compatibility严格到变态啊，祖传代码真的是删不得？


Q2: 在特定工况下的集群，是不是paravirtualization直接操作硬件优势又回来了？比如GPU training?




====

Note:


Recap:

How to virtualize privilege and sensitive instruction?

option 1: Complete Machine Emulation

option 2: Trap-and-Emulate


x86 difficulties: 

1. Not all sensitive instructions are privileged in x86.

2. Hardware-managed TLB gives Hypervisor no chance to intercept on TLB misses

3. X86 has non-tagged TLB. full-flush is needed


VMware Full Virtualization Solution:

(CPU)dynamidc binary translation + (Memory) shaow page table and pmap.


but the question is, why even try if x86 is not designed for virtualization?

+ complexicty 

+ also lost sense of time for the guest OS



不如直接打洞优化guest OS（ParaVirtualization）

* Tradeoff between improved performance with slight modifications to the guest operating system.

* Expose the existence of hypervisor to guest OS, rather than fool them.


ParaVirtualization Design Principles

1. Virtualize all architectural features required by existing standard ABIs. Why is this important?

不用改就能直接跑

2. Supporting full multi-application modern operating systems is important.

服务器就是多application环境

3. Paravirtualization is necessary to work on uncooperative machine architectures such as x86.

x86还是主流




Transparency vs. Optimization

“Keep secrets of the implementation. Secrets are assumptions about an

implementation that client programs are not allowed to make... Obviously, it is easier

to program and modify a system if its parts make fewer assumptions about each

other.”

“One way to improve performance is to increase the number of assumptions that

one part of a system makes about another; the additional assumptions often allow

less work to be done, sometimes a lot less.”


Xen chooses optimization while VMware chooses transparency. 

如果提前优化是万恶之源，那什么算是提前？


每个OS都要port，而且每个版本都要更新这个工作量太大了。如何同步linux的新的feature到Xen port上是生态问题。如果都是整一套，跟主流的KVM发展很难合并.



CPU Virtualization

Control Transfer: Hypercalls and Events

Interrupts= > events

Real time vs Virtual time


Memory Virtualization



I/O virtualization techniques?

1. Direct access to the device

2. Emulating the device

3. Para-virtualization

4. Hardware assist


Xen handles I/O virtualization using I/O ring and asynchronized event delivery mechanism.





Reference:

https://www.zhihu.com/question/421006906/answer/1472983649

还有一个对Xen来说很费劲，但是对KVM来说是一个比较容易的事情，就是对容器化和Serverless的支持，或者说轻量级虚拟机。正式因为KVM是Linux的一部分，才催生像Firecracker，Kata Container这样的项目。

https://zhuanlan.zhihu.com/p/33324585

Domain0: 这是Xen在初期引入的一个特权Dom，Xen Hypervisor在收到IO请求后，需要先把请求投递到Domain0，完成调度处理后，通过grant copy或者grant map转发到对应的虚拟机，相比KVM, 整个IO处理路径几乎被拉长了一倍。其次, x86_64的ring模型相比早期的x86_32也发生了较大变换，从而导致ring压缩，进一步恶化了中断处理的性能。


必须重复造轮子： 最新10年来，CPU已经从单核逐步走向了双核，四核，甚至是几十核心，NUMA技术，TB级内存也基本成为现代服务器的标配，众多厂商和Linux社区在内存和CPU调度和管理上做了大量的工作，而Xen Hypervisor采用独立的CPU和内存调度管理，核心实现还停留在Linux 2.4时代，经过了10年的发展后，根本无力去同步这么多的更新，我们今天会发现，Xen已经落后的太多了，比如：

hugepage：Xen只能提供2M物理页面，而DPDK需要1G的连续物理内存，这是DPDK不能支持Xen的最主要原因。

KSM：透明页面共享。

多核(>128 CPU)调度: 虽然宣称能支持最大192+ core, 但是实际我们发现如果在128 core的4P服务器上创建大规格虚拟机并在其中使用高精度时钟，导致虚拟机频繁陷入陷出调度cpu，Xen就会出现严重问题，这显然是Xen没有经过大规模商业实践的表现。


https://time.geekbang.org/article/9519

