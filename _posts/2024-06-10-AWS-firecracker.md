---
layout: post
title: Unikernels
---

Happy Memorial Day weekend! ** reflection

Q: Name one benefit and one drawback of compiling a single-image VM.

A: Pro: reduced overhead, better performance, smaller attack surface, and footprint.

Con: It requires a deep understanding of the system, which is time-consuming and complex. Reimplementing the existing tooling would be quite some work.


Q: Unikernel runs in a single address space. Give one example of how this design helps improve performance.

A: for example, system call, context-switch + kernel execution + context-switch back.

If we assume only the user or only one kind of application will use the system (no time-sharing), we can remove the barrier of user/kernel mode: direct function call with lower latency.


Q: Comparing gVisor and Unikernels, which one do you think is more secure and which is more lightweight?

A: gVisor is a "firewall" and a "sandbox" for system calls, while unikernal is a dedicated sealed box. unikernal is more secure and lightweight.

[Optional] you can also list the questions you have in the paper. In the meetup, I will go through the questions in FIFO order. 


Q: How would you define "what the application needs"? Building unikernel requires a fundamental end-to-end understanding of the hw and sw, from system requirements to applications to the underlying OS. Everything is in one address space. Anything that can go wrong will go wrong; when it goes wrong, who will fix it?


Q: The no-privilege difference between the application and libOS is hard to sell for average developers. Security is not a feature. The least privilege principle should still apply even if the application has a simple purpose. 

Robust的系统必须是即防坏也防呆的。不然没法scale。软硬件统一开发只适合强势vendor, the end goal is stable and require high performance。 network server, k8s?




Note:


** Unikernels: Library Operating Systems for the Cloud


Software today is complex, even with a single purpose.


More layers -> tricky config

Duplication -> inefficiency

Large size -> long boot times

More stuff -> larger attached surface


Approach:

Disentangle app from OS.

break up OS functionality into modular lib

Link only what's app needed

one codebase = OS(mirage runtime) + app(applogic)


libOS (Exokernel):  is an old idea aiming to expose more hardware interface directly to applications running in user space (自主能动性)

hardware + hypervisor + vm(which is unikenerl: libOS + app)


Unikernel design:

configuration as part of the compilation.

Single-purpose libOS VMs perform only what the application needs and rely on hypervisor for isolation and resource multiplexing (how to define what the application need?)

Within a unikernel VM, there’s no privilege difference between application and libOS (single address space) (oh…wtf?)



3 abstract layer, hypervisor, os process, docker

problem: overgeneralized system: running vm on hypervisor

the kernel is here to protect the user

permission model from the time-sharing system.



Why? Natural evolution. Compatibility over efficiency.


问题在于太费程序员 我连我自己的代码都不相信，整个项目的代码有统一的权限？要是人人都能高效的合作，还开发什么OS，要什么权限系统。


** Unikernels as processes: run app+libOS as processes on host OS, limit interface to OS for security

Can use existing tooling, more flexible, 

把OS的功能挪到process里面
