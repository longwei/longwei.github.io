---
layout: post
title: reflection on the x86 virtualization memory
---

Paper Reflection:

Q: List at least one pro and one con for software MMU and for hardware MMU
A: software MMU: Flexibility but lack of performance, flush shadow page table on each CR3 change.
hardwar MMU: high performace but complex (amplify the memory access x 20, stress on TLB) and $$$ to implement (bigger TLB)


Q: What is the double paging problem and what caused it?
A: page swap出去了，又跳进来了，又跳出去了。
为啥？两个领导不好伺候。Guest and meta-level policies may clash, resulting in double paging


Q: What is the benefit of keeping a "hint" entry for each scanned (but unshared) page (as compared to not maintaining anything for the page)
A: how quickly decide a page is identical to avoid full scanned and re-scan.? 
the idea is to hash content for the signature.

Questions:
1. page sharing 的时候会有hash collision的问题么？如果有，如何避免data corruption 和data leak的问题。
2. memory ballooing 本质是会哭的孩子有奶吃，如何避免noisy neighbor problem，特别是在云端multi-tenant的环境下?
3. how to decide upper limit of number of VM to provision on a server? how to maximum the overcommitment? 切分逻辑是动态的还是固定的？


Note:
* from ppt
** sw vs hw memory virtualization


if TLB hit: sw and hw is the same. MMU generate a page fault if invalid
OS performs page fault handling: fetch the page, update the page table and resume the execution.

but if TLB miss,
sw controlled tlb: HW raise exception, trap to OS, and OS refresh the page table => TLB. 
hw controlled tlb: mmu refresh the page table => TLB. 

知识点： TLB is (hardware cache) subset of page table(data structure in RAM).


** Difficulty in Virtualizing Hardware-Managed TLB
why a hypervisor doesn't have a chance to intercept TLB misses？
it's because TLB misses are typically handled directly by the hardware MMU. When a TLB miss occurs, the MMU initiates a page table walk to fetch the missing translation from memory. 
the hypervisor operates at a higher level of abstraction and does not have direct control over the MMU's low-level operations.

solution:
1. shadow paging (this slide)
2. para-virtualization: modified guest OS, eg. gues OS remove sensitive but unpriviledge insturctions
3. new hw (this slide)

shadow paging
1. VMM 监控CR3, base address of the virtual address spaces
2. CR3 变了，也意味着shadow page table需要re-sync
3. 每个guest physicall address需要重新翻译为machine address
4. 在把CR3指向shadow page table

recall: logic page number -> physical pages number -> machine page number
shadow page table and guest page table per application and pmaps per VM.
shadow pages table can also have user/kenel split


** Hardware-assisted memory virtualization

Intel Extended Page Table (EPTE), referenced by the EPT base pointer.
PPN -> MPN per vm
HW directly walk the guest page table and the extended page table.
不用自己维护shadow page table. 硬件自己re-sync.


** Memory management
memory mamagement: reclaiming, sharing, allocation

*** Reclaiming Memory
total memory size of all VM > actual machine memory size b/c overcommitment.

Requires “meta-level” decisions: which page from which VM to swap and knowledge of guest OS.

solution: implicit cooperation

*** Ballooning
dynamically adjusts the memory allocation for each virtual machine (VM) based on the memory pressure.

** memory sharing
shared same OS, apps or shared memory shm. copy-on-write.



* Performance Evaluation of Intel EPT Hardware Assist
nice visualization of sw MMU and hardward MMU



* Memory Resource Management in VMware ESX Server


** physical is "physical" in the context of virtualization.
pmap: "physical" page number to machine page number

** chap3: Reclamation Mechanisms
3.1 Page Replacement Issues: why double paging problem
3.2 Ballooning: dynamic and also pop the ballon, reset the mapping PPN->MPN.
3.3 demand paging: When ballooning is not possible or insufficient, the system falls back to a paging mechanism

** Sharing Memory
4.1 Transparent Page Sharing: knowledge-based redundant detection
4.2 Content-Based Page Sharing: content-based redundant detection from hint-frame



















