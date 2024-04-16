---
layout: post
title: reflection on the virtualization io
---

Paper Reflection:
Q: Is virtio a full virtualization or a paravirtualization technique? What's its main benefit?
A: It is paravirtualization because the hosted OS was modified to talk to the hypervisor.
Main benefit: simplify and offload virtualization workload to the guest OS.
problem space from (n*m) => (n+m)

Q: List at least one limitation of SR-IOV
A: Hardware-assisted, so compatibility issue required dedicated hardware, OS, and hypervisor driver support.

Q: What are the similarities and differences between network virtualization and traditional server virtualization?
1. similarities:
a. resource isolation, flexibility for easy management, and scaling.
b. previous tech stack didn't consider virtualization.
3. ip address 也是虚拟ip，在Virtual Layer 2 (VL2)做routing

2. differences:
a. 虚拟化 CPU， Memory, IO vs 如何虚拟化switches, routers, and topo to enable virtual machine mobility
b. Software-defined networking SDN and network virtualization overlay NVO tasks, such as network configuration and provisioning, are different.

Question to discuss:
Q: how to acquire/release the lock on the virtio? It is not in the virtqueue_ops API.
Q: how to handle the interrupt concurrence? What happens to interrupt or interrupt?
Q: SDN 也太复杂了，就为了在另一个routing domain里面起虚拟机。一个subnet开大点不可以么？
   比如AWS起EC2都要指定subnet的，似乎规避了这个问题。


# PPT
IO virtualization goal: multiplexing devices across guest VMs
challenge: each guest OS has its own version of the drivers.

## Device virtualization
solution:
* Direct access: VM exclusively owns a device 每个device只能被一个vm用，而且直接打洞穿透虚拟层
* Device emulation: VMM emulates the device in software 读写给emulator，emulator再和device sync。
* Para-virtualization: split driver into guest part and host part。标准化front-end/backend driver (virtio)
* Hardware assisted: hardware devices offer isolated “virtual interfaces (SRIOV)

### SR-IOV (Single Root I/O Virtualization)
single physical PCI Express (PCIe) device to appear as multiple separate physical devices
不再通过hypervisor, IOVM维护mapping信息，之后各个VM再跟NIC直接联系(DMA or signal)


## storage virtualization
A virtual disk is just a file in host file system; Hypervisor maps disk blocks to file offsets

## Network virtualization
Problems: • A physical topology is hard to support different virtual topologies
Virtualized workloads stay in the physical network address space (L2)
用sdn controller和 vSwitch直接控制routing
(没看太明白)



# virtio

problem: too many implementations across block, network, console driver layer
solution: common API and shared data structure vring.

## ch2 3 goal,
* kernel only
* Provide a common ABI for general publication and use of buffers
* two complete ABI implementations using the virtio ring infrastructure and the Linux API for virtual I/O devices

## ch3
common API for transport abstraction for all virtual devices
virtio_config_ops配置
1. feature bits: 支持什么功能
2. configuration space: Guest-readable virtual device
3. status bits: device probe, such as VIRTIO_CONFIG_S_DRIVER_OK
4. device reset


### 3.1 virtqueue_ops

IO add data: add_buf, get_buf
IO notify: kick, disable_cb, enable_cb
it is up to the caller to ensure that they are not called simultaneously


# High-Performance Network Virtualization with SRIOV
## ch1:
SRIOV removes major VMM intervention for performance data movement and cleavage Direct I/O, sharing major device resources.


## ch2:
From the software point of view, a device interacts with the processor/software in three ways
– namely, interrupt,register and shared memory

Physical Function (PF)， A standard PCIe function associated with multiple VFs
Virtual Function(VF)： Each VF is isolated from other VFs， Has dedicated access to certain hardware resources
IOVM, sidecar
The IOMMU page table is used for memory protection and address translation in runtime DMA transactions.

## ch3 Detail of architecture and workflow
The sender writes a message to the mailbox and
then “rings the doorbell”, which will interrupt and notify the
receiver that a message is ready for consumption. The
receiver consumes the message and sets a bit in a shared
register, indicating acknowledgment of message reception.


# Software-Defined Networks ch 8
IP routing的'context'在于网络topo， subnet, mac address...etc
the problem is that virtualized workloads stay in the physical network address space

Goal: How can I create a VM freely?
speed: it exposed the fact that network configuration was now the “long pole”
virtual machine mobility: limits where it can move within the datacenter, which affects the efficiency of resource usage


Solution:
Virtual Layer 2 (VL2) network, such that the addresses used by virtual machines are decoupled from the addresses used in the physical network

"The core principles of SDN—separation of data plane from control plane, and centralization of the controller to manage a multitude of switching elements—provide the basis for this approach" 这也是一个系统设计的通用pattern。