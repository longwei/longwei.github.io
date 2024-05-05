---
layout: post
title: k8s and gVisor
---

TODO: Pre-Meetup Questions from CSE 291

Q:What is a Kubernetes Pod? How do you think it is useful in container orchestration?
A: Pod is the smallest deployment unit in k8s; it groups containers.
It shared resources such as volumes or networking. Pod is ephemeral(stateless)


Q: What does Kubernetes use etcd for? Why is having a consistent, atomic key-value store important for Kubernetes' control plane?
A. the core of the distributed system is the consensus.
   We need to keep the configuration/state/metadata etc, synced in a database ==> etc+database.


Q: Vulnerabilities in the Linux kernel makes it unsafe for containers to call Linux system calls. How does gVisor solve this problem?
A: 1. trap system calls and isolate lots of them in the user space. ptrace/kvm
   2. sandboxing, rule-based filter access to resource, reducing attack surface


Question:
1. 为什么要暴露Deployment这个概念，一个service都应该有scaling, rolling updates, and rollback这些功能的啊？
   或者说lifecycle和network感觉是是同一层面的抽象。
2. how does the service provide a consistent endpoint for accessing the pod?
   How is routing decided if the pod is stateless and can be dynamically created and deleted?
3. how do k8s handle concurrent access to the persistent layer? Is PV the bottomneck of the cluster?

------------------------

Note:
Slide:
we need more than just packing and isolation (docker)
* scheduling: where the containers run?
* lifecycle and health
* discovery: where is my containers
* monitoring:
* authn and authz
* aggregates: containers into jobs
* auto scaling

K8S let user manage applications, not machines.

pod = containers,
deployment = pods + scaling and rolling update; (lifecycle of Pods)
service = deployments + access endpoint + load balancing (networking features)

* k8s overview
container
pod: tightly coupled container
labels/selector
controller: a reconciliation loop
service: a set of pods that work together

* k8s interface
== control plane componenets ==

** kube-apiserver
Acts as the gatekeeper to the cluster by handling
authentication and authorization, request validation,
mutation, and admission control in addition to being the
front-end to the backing datastore


** kube-controller-manager
Monitors the cluster state via the apiserver and steers the cluster towards the desired state


** kube-scheduler
Component on the master that watches newly created pods that have no node assigned, and selects a node for them to run on

Factors taken into account for scheduling decisions include individual and collective resource requirements,
hardware/software/policy constraints, affinity and anti-affinity specifications, data locality, inter-workload
interference and deadlines


** cloud-controller-manager

** etcd


== Node components ==
kubelet
An agent that runs on each node in the cluster. It makes sure that containers are running in a pod.


kube-proxy
Manages the network rules on each node.
performs connection forwarding or load balancing for Kubernetes cluster services.


Container Runtime Engine
A container runtime is a CRI (Container Runtime Interface) compatible application that executes and manages containers

* gVisor
基于namespace/cgroups的隔离并不完全。
** containers do not contains

still sharing the same kernel (seperate network interface but the same TCP/IP stack)
same drivce drives
cgroup may not be accurate

system call are not secure


** compare with vm
vm has independent guest kernels/virtual hw interface

** gvisor
想法类似VM，在host kernel和application之间加防火墙。
sandboxing with filter

内有两个
senctry: emulate linux system call in user space
gofer: file access



reading:
*** Borg, Omega, and Kubernetes

Borg for long-running and batch. linux有个cgroup后，合并了另外两个系统。
在此需求又加入了:
These systems provided mechanisms for configuring
and updating jobs; predicting resource requirements;
dynamically pushing configuration files to running jobs;
service discovery and load balancing; auto-scaling; machinelifecycle management; quota management

omega 是borg的重写。
Omega stored the state of the cluster in a centralized Paxos-based transactionoriented store that was accessed by the different parts of the cluster control plane.
!using optimistic concurrency control to handle the occasional conflicts, avoid funneling every change through master.

k8s 是第三次，要卖云服务。
特点在于把persistent store直接暴露给control plan，把运维转变为定义期望状态的control loop.

A modern container is more than just an isolation mechanism: it also includes an image—the files that make up
the application that runs inside the container

== Application environment, not machine!
Containerization transforms the data center from being machine-oriented to being application-oriented.
the key is to have a air tight container that encapsulate all external dependencies would be linux kernel system call.
then limit the surface of socket, /proc and ioctrl

== Containers as the unit of management
This data enables the development of generic tools like an auto-scaler or cAdvisor3
that can record and use metrics without understanding the specifics of each application. Because the container is the
application, there is no need to (de)multiplex signals from multiple applications running inside a physical or virtual machine.

PS: why it is not 1:1 mapping to container
co-scheduled on the same machine, the outermost one provides a pool of resources; the inner ones provide deployment isolation
...so Kubernetes regularizes things and always runs an application container inside a top-level pod,even if the pod contains a single container

==Orchestration is the beginning, not the end
一旦把计算和机器分离, unlock lots of oppotunities:
* Naming and service discovery
* Master election, using Chubby
* Application-aware load balancing
* auto scaling
* Rollout tools that manage the careful deployment of new binaries and configuration data
* Workflow tools (e.g., to allow running multijob analysis pipelines with interdependencies between the stages).
* Monitoring tools to gather information about containers, aggregate it, present it on dashboards, and use it to trigger alerts

Decoupling ensures that multiple related but different components share a similar look and feel:
* ReplicationController: run-forever replicated containers (e.g., web servers).
* DaemonSet: ensure a single instance on each node in the cluster (e.g., logging agents).
* Job: a run-to-completion controller that knows how to run a (possibly parallelized) batch job from start to finish.

== THINGS TO AVOID
* Don’t make the container system manage port numbers, assign unique ip to each pod.
  aligning network identity (IP address) with application identity
* Don’t just number containers: give them labels.
  labels are inherently more flexible than explicit lists of objects or simplestatic properties
* Be careful with ownership (controllers<-->pods, jobs and task)
  pod-lifecycle management components such as replication controllers determine which pods they are responsible for using label selectors, so multiple controllers might think they have jurisdiction over a single pod.
* Don’t expose raw state
  It does this by forcing all store accesses through a centralized API server that
  hides the details of the store implementation and provides services for object validation, defaulting, and versioning

== SOME OPEN, HARD PROBLEMS
* Configuration
  configuration should be simple, not DSL code or Turing complete.
  "It doesn’t reduce operational complexity or make the configurations easier to debug or change; it just moves
  the computations from a real programming language to a domain-specific one, which typically has weaker development
  tools such as debuggers and unit test frameworks. "
  说的太好了，内部的DSL最后结局都是没人维护。
* Dependency management
  If an application has dependencies on other applications, wouldn’t it be nice if those dependencies (and any transitive
  dependencies they may have) were automatically instantiated by the cluster-management system
  A standard problem is that it is hard to keep dependency information up to date if it is provided manually and at the
  same time attempts to determine it automatically



*** The True Cost of Containing: A gVisor Case Study
overhead 还好，15mb+150ms
适合small, spin up quickly & high density ==> lambda

不适合：
1. trusted image就没必要pay perf tax
2. syscall 多 或者 direct access to HW
3. gvisor不支持的的syscall




