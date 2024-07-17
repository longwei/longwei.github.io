---
layout: post
title: KVM and QEMU
---

TODO: please write a 300-word paper review report that summarizes what you learned from this paper, what impresses you the most, what the technical flaws are, and how it compares to other systems you knew before reading the paper. 


TODO: Pre-Meetup Questions from CSE 291
1. What is the implication of KVM forwarding I/O requests to the user space?
How: when VMM see a IO request, it will forward it to user space. user space handle it in device model.
Implication: KVM's MMU does not create shadow page table, it is the emulator execute the faulting instruction to calc the transfer details. IO information is implicited shared per IO request.


2. What is the benefit of QEMU first translating the source instructions (guest) into micro-operations implemented in C and their compiled object files and then translating the object files into the target instructions (host)?
A: C is much more portable than ASM for each host. 
QEMU first translates target CPU instructions into a sequence of simpler micro-operations in C.(human),
then those C code will compiled into machine code (compiler).


3. Can you think of some good use cases for QEMU+KVM?
KVM focuses on CPU and memory virtualization, utilizing hardware features for near-native performance. 
QEMU focus device emulation capabilities such as hard drives, network cards, and graphic adapters.

The combination of both and simplify the server fleet, so that we can more freely to schedule the work without too much limitation.





==Note==
KVM – the Kernel-based Virtual Machine – is a Linux kernel module that turns Linux into a hypervisor.
Close integration with Linux. Reuse Linux scheduler, memory management, etc.


/dev/kvm Device Node

* Creating a new VM 
* Allocating memory to a VM 
* Reading and writing virtual CPU registers 
* Injecting an interrupt into a virtual CPU 
* Running a virtual CPU

Hardware-Assisted CPU Virtualization: Intel VT-x
* added non-root mode: sensitive instruction will switch to root mode.
* new hardware structure. VMCS

KVM Execution Model
* User mode 
* Kernel mode
* Guest mode


* How do QEMU and KVM differ?
While both are involved in virtualization, they operate at different levels:
QEMU can function as a full-system emulator, translating target CPU instructions to the host machine's language, even without hardware assistance. This approach, however, can be slower.
KVM, on the other hand, requires hardware support and works directly with the Linux kernel to run the guest OS in a more native-like environment, leading to significantly improved performance.
