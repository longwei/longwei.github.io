---
layout: post
title: container meetup class 5
---

# Pre-Meetup Questions from CSE 291 #

What types of isolations do Linux containers achieve?
1. from namespace
* user namespace: UTS namespace or user namespace
* Process: PID namespace
* file path (mount namespace)
* Network namespace
* pipe names or share memory (IPC namespace)
2. from cgroup
* resource (CPU, memory, etc)

Can one Linux container affect the performance of another Linux container on the same machine (i.e., performance isolation)? Why or why not?
Yes, that's why we need resource-level isolation, (ulimits/rlimits?)
同一台机器跑的程序不是一定好人写的。就算是好人写的也可能有bug。

Why do you think containers are less "secure" than virtual machines?
vm是先分家，在考虑性能问题. No by default.
container是先性能问题，再考虑分家问题。Yes, by default.
Container is just another process, after all.
1. still share the same kernel
2. less control on isolation and syscall

# reading note #:

## ch2 ##

### services:
main driver:
* including better economy for (PaaS) providers,
* enabling better development pipelines,
* easier to testing.
* better security

google, everything in container? even DB or stateful application?
KVM+LXC, and k8s
vmware, even openstack(?)
and many more. 生态很丰富

### clients
Linux sandboxing
* easily limit CPU, Disk and Memory for everything from resource hungry (and highly exploited) web browsers to anonymous liberation technology systems which can be critical to secure from indirect information leaks.

时代的眼泪：自己项目的坑，在linux上deployment end user facing的applicaiton很烦，现在都上Electron了。

Chrome OS Yes!
kernel Namespaces (Network and PID), Seccomp-bpf, SUID sandboxing, or in newer versions full user namespaces

Android No!
mostlyon Discretionary Access Controls (DAC) aka UNIX permissions and other enforcement via a normal UID and process isolation based security model.


### Prior Art: Linux Container Security, Auditing and Presentations
扩展阅读方向：
* Multi-Container
* LXC Specific
* Docker Specific

### Linux Containers
five components:
1. Kernel Namespace ch3
2. control groups cha4
3. root capabilities cha5
4. pivot root
5. ManditoryAccessControls(MAC)


## ch3 namespace 如何创建虚拟世界##
### background
kernel namespaces form a foundational isolation layer that allows for the implementation of Linux containers by creating different userland views.
why?互不影响
how? namespace + cgroups

### implementation
clone() CLONE_NEW
isolation 是多个一个纬度的概念，下面介绍各个namespace

### mount namespace
such as private mount point,etc

### IPC namespace
creating objects in an IPC namespace which are visible to all other processes that are members of that namespace, but are not visible to processes in other IPC namespaces

### UTS namespace
unix time share allow a single system to appear to have different host and domain names to different processes

### PID namespace
All PID namespaces start at PID 1, the traditional init. Subsequent calls to fork(2), vfork(2), or clone(2) will produce processes with PIDs that are unique within the namespace
八股文最常考的部分

### Network namespace
everything that relates or supports these features including de- vices, addresses, routing, firewall rules, procfs directories, and sockets among other requirements
bridge-style virtual network interface (NAT)
网络也共享，收发室怎么修改。


### user namespace
Until user namespaces, the root user, even when restricted using Linux capabilities, was still uid 0 as far as the kernel was con- cerned.
你的root不一定是我的root

`Containers can have their own user and group IDs, separate from those on the host system. This provides an additional layer of security by isolating the container's users from the host system.`


## ch4 control group ##
### cgroup background
To put it simply, cgroups isolate and limit a given resource over a collection of processes to control performance or security.
older way ulimits/rlimits(?)
authn & authz & rate limit 鉴权和限流

When reviewing cgroups, it can be easy to imagine them as a method to fill the gaps of kernel namespaces. However, it is important to remember that cgroups are not namespaces, nor are they really intended or invented for containers; cgroups are simply one property of processes.

### 子系统：
CPU:
Memory:
BLKIO:
Devices:
Network:
Freezer(?)
PID

### Containers and cgroups
As part of a container system, cgroup management is typically abstracted away


## ch5 capabilities ##
### Capabilities Background
the uid 0 (zero) user aka root, has complete control over the system
and there is more than one way to flip the bit to 0

```The problem with this design is that programs which are running setuid exist in two realms at once and must attempt to be both a privileged service provider, and a tool available to users - much like the confused deputy```

类似IAMROLE?


### Extend reading TODO
Linux Capabilities: making them work
How Linux Capabilities Work (in 2.6.25) by Wenliang (Kevin) Du

### Understanding Capabilities
列举了root能干的事


### Capabilities and User Namespaces
root in user namespace escape to be the root of the whole universe.
Yes, it is root of that what this namespace can do
No, it can't do anything else.

类似AWS Organizations， 和Service control policy的eval规则

### Capability Defaults In Modern Containers
not all containers are equal:
* LXC is understood to commonly run entire virtual operating systems, and expects the administrator to tune the templates appropriately
* Docker, developers are expected to follow the newly established convention of running single applications, also referred to as "App VMs" and Docker itself must appeal to the developer masses by allowing the largest default set of capabilities that does not put the system at a risk

###  A World Without Root
不如不用root，更精细的priviledge? 类似AWS不用root，全用IAM














