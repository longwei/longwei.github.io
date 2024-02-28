---
layout: post
title: reflection on the x86 virtualization memory
---






# from PPT:


## sw流派sw1： 模拟器
创建了虚拟的emulation layer来转译instruction。
限制： 效率低：快不了。类比valgrind。


## sw流派2: Direct Execution with Trap-and-Emulate. 
上次的virturalization的文艺复兴之作。
限制：只有CPU可行。而且是都是同一指令集

### x86 系统的虚拟化难在哪里？
native有两种instruction需要额外关心：
1. privileged instructions: those that trap when in user mode
2. sensitive instructions: those that modify or depends on hardware configs

x86的问题在与，
1. 以上两个子集不重叠。
有些instruction改了senstitive instruction，但不是privileged
2. 另外有些instruction行为不一致，popf 

### 本能想到的是，两个子集之union的都会触发trap? 
即：又对operators(instruction)做trap， 也对operands(protect data)做trap
再单独处理sensitive but not privileged的特例
后面的Binary Translation看确实是这个思路, 不过都做了动态翻译了，直接把不同ISA也翻译了吧。

## sw流派3: Direct Execution with Binary Translation
goal: guest os not modified and full virtualization
how: dynamic binary translation, trap all non-virtualzable instruction and emulate it by other sequence of instruction.

问题：怎么其他x86 instruction set来翻译其non-virtualizable instruction。刚好能做到等价？
这里估计是vmware的trade secret部分，这部分的工程量爆表。
又要考虑instruction 不用ring下的behavior不同，还要做到instruction set之间的翻译。

Non-Ident: 没法直接翻译
1. PC-relative address
2. Direct control flow
3. indirect control flow
4. sensitive instruction

### Adaptive Binary Translation:
* Start in the innocent state and detect instructions that trap frequently
* Patch the original IDENT translation with a forwarding jump to the new translatio
这里的意思是边跑边放出翻译的版本？因为没法static 判断是否需要trap?

## 硬件流派1: Direct Execution with Hardware-Assisted Virtualization
1. VMX non-root mode: runs VM, sensitive instructions cause transition to root mode, even in Ring 0
终于分别区别对待privileged vs sensitive
2. vm control structure

简而言之，硬件需要感知vm的存在，才能对其更好的支持


## 其他流派：
- Paravirtualization。focus定制guest OS
- container， k8s
- 语言层，JVM


# from A Comparison of Software and Hardware Techniques for x86 Virtualization:
## 为什么第一代virtualization硬件支持的性能不如意？有些指标还不如sw？

1. 本身没得直接加MMU，
2. 然而又没有和sw的 mmu virtualzation适配, 还是各自为政

compute-bound 表现都不错
IO-bound SW表现更好。
两者mix： HW表现更好。


## chapter 2 & 3: recap
* De-privileging: aka trap-and-emulate
* Primary and shadow structures, aka maintain your own TLB 
* memory trace: sync Primary and shadow structures by CR3 register 
* highlight 
"In our experience, striking a favorable balance in this three-way trade-off among trace costs, hidden page faults and context switch costs is surprising both in its difficulty and its criticality to VMM performance.""
binary translation and Adaptive Binary Translation


## 用hw需要改动什么？
chapter 4: their hw enhancement
1. virtual machine control block, or VMCB
2. A new, less privileged execution mode, guest mode
3. A new instruction, vmrun, transfers from host to guest mode


## 如何定量分析这个问题，来判断到底哪些部分需要hardware acceleration呢？
具体到db或者web server这种user pattern不同的情况呢？
chapter 5: 指标定义
chapter 6: compare and why

## chapter 7: 有哪些可以改进的地方
1. Microarchitecture: 没看明白。 为什么Microarchitecture可以让page fault exit更快。
2. Hardware VMM algorithmic changes。 利用算法避免exit.
3. 进一步集成sw/hw of VMM
4. 最有希望的是硬件加MMU support for VMM

* 补充：what's page fault: physical memory 超卖了, swap back from disk.




















