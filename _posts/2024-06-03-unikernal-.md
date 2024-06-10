---
layout: post
title: Firecracker
---

Amazon Firecracker

TODO: Pre-Meetup Questions from CSE 291

Q: What is the benefit of Firecracker over gVisor in terms of the specific goals Amazon has for their cloud production environments?
A:
1. Strong isolation and compatibility: The VM model has strong isolation and security; it doesn't rely on re-implementing the sandbox kernel in user mode.
2. Resource efficiency: higher density and startup time for multi-tenant applications such as lambda and Fargate.

background;
Firecracker: a VMM based on KVM to create and monitor microVMs. 
gVisor: container-based user-space kernel that "sandbox" syscall and emulate them in user space.


Q: What mechanism(s) allow Firecracker to run thousands of MicroVMs on the same machine (with 10x-20x oversubscription rate)?
A:
1. Minimal design with constraints: There is no BIOS, PCI, USB, or Video, just the essentials for an immutable, time-limited micro VM. 
2. Soft allocation. Memory is dynamically allocation on demand. CPU slice is also soft allocated.



Q: Why do you think Firecracker (when deployed to power AWS Lambda) run one process (one slot) in one MicroVM?
A: 
1. security and isolation. "process" is a strong isolation model from OS. Good for the muti-tenancy user case.
2. resource allocation and scheduling: lots of existing tooling for this problem and don't need to re-invent the wheel.
3. performance: Reusing the process model is good for performance as it is more native without overhead.

Slot definition: "with each slot providing a pre-loaded execution environment for a function. Slots are only ever used for a single function, and …(re-usable)..."

[Optional] you can also list the questions you have in the paper. In the meetup, I will go through the questions in FIFO order. 
1. What kind of optimization does Firecracker have done compared to crosVM/QEMU? Requesting code walkthrough session :)
(The entire virtio block implementation in Firecracker (including MMIO and data structures) is around 1400 lines of Rust)



Note:
1.1 Specialization
Firecracker was built specifically for serverless and container applications. 
Firecracker is probably most notable for what it does not offer, especially compared to QEMU. It does not offer a BIOS, cannot boot arbitrary kernels, does not emulate legacy devices nor PCI, and does not support VM migration.

Firecracker’s process-per-VM model also means that it doesn’t offer VM orchestration, packaging,
management or other features — it replaces QEMU, rather than Docker or Kubernetes, in the container stack

1.2 Choosing an Isolation Solution
Isolation: It must be safe for multiple functions to run on the same hardware, protected against privilege escalation, information disclosure, covert channels, and other risks.
Overhead and Density: It must be possible to run thousands of functions on a single machine, with minimal waste.
Performance: Functions must perform similarly to running natively. Performance must also be consistent, and isolated from the behavior of neighbors on the same hardware.
Compatibility: Lambda allows functions to contain arbitrary Linux binaries and libraries. These must be supported without code changes or recompilation.
Fast Switching: It must be possible to start new functions and clean up old functions quickly. 
Soft Allocation: It must be possible to over commit CPU, memory and other resources, with each function consuming only the resources it needs, not the resources it is entitled to.

2,1 Compare with others:
1. QEMU/KVM: density and overhead challenge
2. Linux containers: isolation and compatibility challenges
3. LibOS: compatibility
4. JVM-ish: compatibility and isolation

3. The Firecracker VMM
* Simple Device Model
We use virtio for network and block devices, an open API for exposing emulated devices from hypervisors.
virtio is simple, scalable, and offers sufficiently good overhead and performance for our needs
Just virtio-net, virtio-block, virtio-vsock, serial console, and a minimal keyboard controller


* REST API over socket.
By contrast, the typical Unix approach of commandline arguments do not allow messages to be passed to the process after it is created, and no popular machine-readable standard exists for specifying structured command-line arguments.

* Rate Limiters, Performance and Machine Configuration. significantly less flexible and powerful than Linux cgroups which offer additional features including CPU credit scheduling, core affinity, scheduler control, traffic prioritization, performance events and accounting

* Security
TO READ:
 With existing CPU capabilities, no single layer can mitigate all these
attacks, so mitigations need to be built into multiple layers
of the system. For Firecracker, we provide clear guidance
on current side-channel mitigation best-practices for deployments of Firecracker in production4
. Mitigations include disabling Symmetric MultiThreading (SMT, aka HyperThreading), checking that the host Linux kernel has mitigations enabled (including Kernel Page-Table Isolation, Indirect Branch
Prediction Barriers, Indirect Branch Restricted Speculation
and cache flush mitigations against L1 Terminal Fault), enabling kernel options like Speculative Store Bypass mitigations, disabling swap and samepage merging, avoiding sharing
files (to mitigate timing attacks like Flush+Reload [61] and
Prime+Probe [42]), and even hardware recommendations to
mitigate RowHammer [28, 43].


* Jailer
The jailer implements a wrapper around Firecracker which places it into a restrictive sandbox before it boots the guest, including running it in a chroot, isolating it in pid and network namespaces, dropping privileges, and setting a restrictive seccomp-bpf profile (seccomp-bpf?) (eBPF?)

4 Firecracker In Production

standard high level overview: front-end->metadata
                                                              ->manager->placement->workers

Firecracker In The Lambda Worker (slot): The MicroManager also keeps a small pool of pre-booted
MicroVMs,

The Role of Multi-Tenancy: Oversubscription is fundamentally a statistical bet…Multi-tenancy is a powerful tool for reducing this ratio, which naturally drops approximately with √ N when running N uncorrelated workloads on a worker.


