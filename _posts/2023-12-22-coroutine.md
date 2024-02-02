---
layout: post
title: 线程 vs 协程？
---

* 线程由中断和特定调用分出/让出时间片和触发上下文切换。切分的是对CPU的占有
* 协程则由 yield/await 等语言结构或特定调用分出 continuations 并切换上下文。切分的是控制流。

切了之后的调度既可以有kernel也可以user space。各种组合都也有。
只是先有了process，那么thread直接照搬，除了process own resource, 而thread依附与process to access the resource.
但是都在一个user space的事，有必要这么粗暴么？交接点非常明确，也就不会有uncommit change.
coroutine本来就没想在CPUs并行。



muti-process能利用CPUs，
但是就算是一个user space， 同样会有等待的问题。
如果直接把process模型拿过来，只是不own context，就是threading
但是threading还是太粗了，如果代码可以任意时间interrupt, 需要花费大量精力带保护 critical section



coroutine的关键是合作，我不放弃，谁也别想赶我走。
虽然还是会被其他process挤走，但是能碰到我的context一样停下，类似事件静止，所以不需要额外保护数据。
利用yield, await, async是execuation switch point,切出来的上下文。

coroutine can be OS-level，但是对跑在上面的程序要求很高，每个进程都要发扬风格, 不能有恶意或者故意不配合。
法律必须是没有例外，Preemptive Scheduling把控制权交还给OS。OS才不管你做到哪里，公平公平，还是公平，不体面就帮帮你体面。
其缺点就是由于随时会被中断+下个执行有可能修改同一份数据，所以必须同步手段(lock)来保护，带来复杂度和context switch.

组(process)之间的协作很难，甚至不需要恶意，只是一不小心就可以导致事故。
组内资源共享，如果是被迫共享，即build（IO事件）会被彼此争抢。那么thread一定要保护性编程(overhead)
但如果isolate组的资源，默认他们会主动合作，那么即便中断，那么数据竞争、执行绪切换等等烧脑/头疼的问题自然就消失了。

coroutine为什么不能Parallel? 因为虽然2个coroutine不再有执行权的切换，但是又会出现data race 问题, 除非没有共享数据。



如果资源存在相互依赖，线程还有必要么？example:
load log, analyze log and send out result
task 1 is io-intensive
task 2 is cpu-intensive
task 3 is network activity

如何最大化利用磁盘，cpu和网卡？
哪些地方可以Parallel可以提高效率？哪些地方并行反而overhead？
如何自动安排合理数目的线程，把磁盘、CPU、网卡同时利用起来？

1. 多路IO场景下，协程已经可以同时发起多个读取请求；
3. 那么如果系统有多块网卡、多块磁盘，OS自然会并行利用它——因为这些接口本来就是异步的，OS会自动给它排队，能并行就安排并行了。

所以1，3 single process + coroutine即可。但是2不是，必须用thread才能利用cpu核心.



** call back hell
coroutine 不一定能解决这个问题，如果callback之间没有依赖关系，这样写的更好看。
但是如果有依赖关系，改了反而会有时序问题。
v1->v2->v3..


** network and concurrent
不允许使用协程，你如何在一个普通的单线程C程序里，用一个while循环，做到多块网卡并行工作，既不阻塞自己、又会在没有报文时主动交出执行权、不空耗时间片呢？
（一个拿了实时优先级的程序空耗时间片可是个非常非常严重的问题，随时可能让整个OS崩掉的那种。）
non-blocking I/O + multiplexing manage multiple sockets in a single thread efficiently

Non-blocking I/O: Setting sockets to non-blocking mode allows your program to perform I/O operations without waiting. When data is not immediately available, instead of blocking and waiting for data to arrive, the program continues to execute other tasks or checks other sockets.

Multiplexing: Functions like select() or poll() enable you to monitor multiple sockets for activity (incoming data, readiness to write, etc.) within a single thread. These functions allow you to wait until one or more sockets become ready for I/O operations, enabling you to handle multiple sockets concurrently without blocking.





ref:
https://zhuanlan.zhihu.com/p/147608872
https://www.zhihu.com/question/533573984/answer/2492439059

