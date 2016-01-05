---
layout: post
title: go and coroutine
---



coroutine 保存register然后跳转到另外一个function

假设cat 10个file， 但是内存不能同时存下。

blocking API: lib必须维护一堆状态， 详情参见read4
non-blocking API: user必须维护callback和状态

无论是同步还是异步，都有这种“维护状态”的麻烦。

use code to maintain the status, not variables. 这就是coroutine
(what's the diff with function with static members?)

IPC

IPC #1. pipe.
#2， socket, only one process handle everything, select listen to IO, first come first serve. by the connection id and type, dispatch to callbacks.
#3, epoll, upgraded select with black/red tree.

coroutine is optimized multi-process, if could have scheduler like go, or not like yield in c#. it have bigger overhead than async callback, but cheap than thread.


协作式调度相比抢占式调度而言，可以在牺牲公平性时换取吞吐, 抢占式调度在响应的延时上更有优势,协作式调度更容易把缓存跑热，在计算任务的吞吐上更有优势

在互联网行业面临 C10K 问题时，线程方案不足以扛住大量的并发，这时的解决方案是 epoll() 式的event-driven，nginx 在这波潮流中顺利换掉 apache 上位。类似的框架还有tornado/nodejs

事件循环的异步控制流对开发者并不友好。业务代码中随处可见的 mysql / memcache 调用，迅速地膨胀成一坨 callback hell。这时社区发现了协程，在用户态实现上下文切换的工具，把 epoll() 事件循环隐藏起来，而且成本不高：用每个协程一个用户态的栈，代替手工的状态管理。似乎同时得到了事件循环和线程同步控制流的好处，既得到了 epoll() 的高性能，又易于开发。甚至通过 monkey patch，旧的同步代码可以几乎无缝地得到异步的高性能，真是太完美了。
然而，跑了一圈回来，协程相比原生线程又有多少差别呢。

1. 用户态栈，更轻量地创建“轻量线程”；
2. 协作式的用户态调度器，更少线程上下文切换；
3. 重新实现 mutex 等同步原语；

协程的创建成本更小，但是创建成本可以被线程池完全绕开，而且线程池更 fine grained，这时相比线程池的优势更多在于开发模型的省力，而不在性能。此外，"轻量线程" 这个名字有一定误导的成分，协程作为用户态线程，需要的上下文信息与系统线程几乎无异。如果说阻碍系统线程 scale 的要素是内存（一个系统线程的栈几乎有 10mb 虚拟内存，线程的数量受虚拟地址空间限制），那么用户态线程的栈如果使用得不节制，也需要同量的内存。

协作式调度相比抢占式调度的优势在于上下文切换开销更少（但是差异是否显著？）、更容易把缓存跑热，但是也放弃了原生线程的优先级概念，如果存在一个较长时间的计算任务，将影响到 IO 任务的响应延时。而内核调度器总是优先 IO 任务，使之尽快得到响应。此外，单线程的协程方案并不能从根本上避免阻塞，比如文件操作、内存缺页，这都属于影响到延时的因素。

事件循环方案被认识的一个优势是可以避免上锁，而锁是万恶之源。协程方案基于事件循环方案，似乎继承了不用上锁的优势。然而并不是。上下文切换的边界之外，并不能保证临界区。该上锁的地方仍需要上锁。

差异存在，但该维护的信息并没有更少。如果运行时对系统线程的支持比较好，业务系统使用协程的综合效益并不一定相比线程池更好。我们业内通常意义上的"高并发"，往往只是要达到几k qps，然而 qps 是衡量吞吐而非并发的指标（并发1k意味着同时响应1k个连接，而 qps 衡量一秒响应多少请求，这可以是排队处理，并不一定"并发"），靠线程池并非做不到。但对 python 这类 GIL 运行时而言，这却拥有显著提升性能的优势了，只是这时瓶颈在 GIL，而不在线程。

至于并发量导向的业务，一般也是状态上下文较少的业务，比如推送，这时 callback hell 基本可控，使用协程相比事件循环依然更容易编程，但效益并不显著。

最后尝试总结一下个人的想法：

协程不是趋势，它是一个在历史中被挖掘出来的、对现有问题的一个有用的补充。

适用的场景：
高性能计算，牺牲公平性换取吞吐；
面向 IO Bound 任务，减少 IO 等待上的闲置，这其实和高性能计算领域内的优势是一致的；
Generator 式的流式计算；
消除 Callback Hell，使用同步模型降低开发成本的同时保留更灵活控制流的好处，比如同时发三个请求；这时节约地使用栈，可以充分地发挥 "轻量" 的优势；
但并不是万灵丹：
如果栈使用得不节制，消耗的内存量和系统线程无异，甚至内存管理还不如系统线程（系统线程可以动态地调整虚拟内存，用户线程的 Segmented Stack 方案存在严重的抖动问题，Continous Stack 方案管理不当也会抖动，为了避免抖动则成了空间换时间，而内核在这方面做了多少 heuristic 呢）；
IO Bound 任务可以通过调线程池大小在一定程度上缓解，目标是把 CPU 跑满即可，这点线程池的表现可能不完美，但在业务逻辑这个领域是及格的；
此外，一般的 python/ruby 任务并不是严格的 IO Bound，比如 ORM 的对象创建、模版渲染、GC 甚至解释器本身，都是 CPU 大户；单个请求扣去 redis 请求和数据库请求的时间，其它时间是否仍不少呢？
CPU 上长时间的计算，导致用户线程的调度变差，不能更快地响应，单个请求的平均时间反而可能更长（诚然并发可能更高）；然而这在 python 这类 GIL 语言来看并不算劣势，甚至比 GIL 的调度更好，至少 gevent 可以知道各 IO 任务的优先级，而 GIL 的调度是事实上的 FIFO；

References

- Xiao-Feng Li: Thread mapping: 1:1 vs M:N
- http://thread.gmane.org/gmane.comp.lang.rust.devel/6479

http://blog.youxu.info/2014/12/04/coroutine/



REF:
http://www.zhihu.com/question/32218874/answer/56255707
http://www.zhihu.com/question/32218874/answer/56255707
