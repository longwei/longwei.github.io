---
layout: post
title: why the design db
---


为什么 Redis 选择单线程模型
1. IO multiplexing这么香， 用select监控多个连接。
2. mutil-thread 的data racing 太麻烦了
3. web server bottleneck 是IO不是CPU
4. 新版本有些可以多线程来做，UNLINK、FLUSHALL ASYNC 和 FLUSHDB ASYNC. 特点是没有data race。


为什么 MySQL 使用 B+ 树
B 树与 B+ 树的最大区别就是，B 树可以在非叶结点中存储数据，但是 B+ 树的所有数据其实都存储在叶子节点中
由于所有的节点都可能包含目标数据，我们总是要从根节点向下遍历子树查找满足条件的数据行，这个特点带来了大量的随机 I/O，也是 B 树最大的性能问题。
B+ 树中就不存在这个问题了，因为所有的数据行都存储在叶节点中，而这些叶节点可以通过『指针』依次按顺序连接

为什么 Redis 快照使用子进程

