---
layout: post
title: note on note on distribute system
---

TL;DR
There are fundamential differences between local computing and distribute computing, those differences cannot be ignored, or project are doomed to failure

Unified View of Objects
===
the keypoint here is the difference in location is hiding as implemtation details.

so the development is spli as:

* decide interface that are location agnostic
* finalize which objects will be local and which will be remote
* test and calibrate it

principle of this unified design:

* there exist a signle, natural design for the application regardless of whether parts of it will be deployed locally or remotely
* failure and performace issues are implementation dependent and not the fault of the initial design
* the interface of an object is independent of the context where that object is used


bascially, this model is built for PM that can't code. the hard problem is deferred to implementation rather that design phase.

4 things that matters
===

* latency
* memory access
* partial failure
* concurrency

local don't have partial failure issue, while distributed system failure of componenet is inevitable. this kind of indeterminism is required for partial fauilure.

the central problem in distribute computing is to ensure that the state of system is consistent after partial failure(See CAP)

No, it is not about QoS
===

robustness/performace is not a function of the implementation, you can't magically have a perfect implementation that fix the robustness/performace issue.


Lesson from NFS
===

* local file system API used for a distributed system
* soft mounting
  * expose network/server issue to client, and may filesystem corruption
* hard mounting(no, you don't want to do that)
  * application hangs until server comes back?
  * chain of frozen workstations?
  
Design with those in mind
====

* specify whether local or remote as part of interface definition, or even better, split it up
* access objects differently while writing code (don't hide it in a wrapper)
  * think C++ cast

A middle ground
===
object in different address space but on the same machine 

* share attribute of both local and remote
* simpler code generation (shared memory etc)

PS: I had a bad year of expericence to back this story, *village. 

that project shows exactly the problem illustrate in this paper from 1994. (sad) I wish the original devs and PMs could read this paper. while looking backward, the problems in project context are: 

* got literrally over 20 ways to configure the application, it is also a nightmare to deploy. 
* heavy dependecies, it import a forest just in order to get a banana.
* split objects into multiple database while logically they should sit together
* because the db scheme to too normalized, 30 nested join query is produced just in order to get data from A to B
* make the interface of rpc too easy, the output is abusively recursive rpc calls for simple request
* too many PM, too little developer
* documentation is not the ultimate answers. 
* oursourcing because you don't bother to do it, not you can't do it.
   