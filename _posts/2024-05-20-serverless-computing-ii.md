---
layout: post
title: Serverless Computing 2
---

TODO: Pre-Meetup Questions from CSE 291

Q: Why isn't using existing in-memory key-value stores such as Redis and Memcached a good option for storing ephemeral data in serverless computing?
A: In-memory is not cost-effective and does not fully utilize the app's pattern(trade-off between latency and throughput, etc). for example, some apps are sensitive and prefer throughput rather than low latency.

Q: How does Pocket balance storage load?
A: It is similar to Apple's Fusion_Drive. It assigns the job to the pool of storage server clusters based on throughput and capacity hints. The servers contain different storage media, such as DRAM, Flash, and HDD.

Q: Do you think Pocket solve all the problems of managing states in serverless computing? If not, what do you think are the remaining problems?
A: No.
1. Pocket assumes the lifecycle with the lambda function call. Anything that needs to live beyond that would use Redis or Dynamodb.
2. Implicitly expecting the user to give the hint can be difficult. How can we reliably and accurately get those hints? 
Finding the pattern would require detailed performance metric profiling or predefined profiles for common workloads.


[Optional] you can also list the questions you have in the paper. In the meetup, I will go through the questions in FIFO order. 

1. Can the scheduling algorithm be further optimized by the actual cost? If so, it would be cost-aware and dynamic resource allocation between standard serverless(AWS) and pocket (DIY). for example, s3 is much cost effective than ec2 with HDD.

2. Since fault tolerance is not the top priority, should we request EC2 spot instances instead? Two minutes of draining is enough for any cleanup.

==========

## Slide
* Serverless is stateless by design, enabling users to launch short-lived tasks with high elasticity and fine-grain control over billing, not scheduling.
* serverless is increasingly used for interactive analytics, which does share intermediate state
* The challenge: lambda needs an efficient way to communicate between stages while users have no direct control over the scheduling => common data store.
* requirement for ephemeral storage: high performance for all patterns and cost efficiency, 
* give up fault tolerance as it assumes the application is resilient and the nature of the analytics job.

Note:
Distributed compilation: There is lots of IO first, then calm down. High parallelism on the independent file initially. 
Map reduce: lots of IO first, then multiple smaller peaks on each shuffle
video analytics: calm first, then lots of IO to take the video in, then decode them

Pocket:
Controller plane (or scheduler in k8s term?). it is the brain
metadata servers (memory): request server, it is mapping logical memory => physical location
Storage plane: physical location

Scheduler workload:
Optional hint: latency, maximum concurrent, total ephemeral data capacity, peak aggregate bandwidth

Autoscaling the pocket cluster:
Monitoring metrics and scaling up and down on resource usage

Pocket is elastic, distributed /tmp


## Occupy the Cloud: Distributed Computing for the 99% (PyWren)
why? 
Where is my scale and elasticity?
data scientists are people, too. The ease of deployment wins. Performance, meh, is a good extra. Users should just submit functions for the serverless execution.

Hadoop and Spark still present high barriers to entry for the average data scientist or scientific computing user.
A vast number of data analytic and scientific computing workloads remain embarrassingly parallel, requiring no communication or dependency between the processes. 

CPU-bound: parallelize across functions rather than within each function a simple function interface that captures sufficient local state, performs computation remotely and returns the result is more than adequate
IO-bound: A simpler version of the existing map-reduce framework, where outputs can easily persist on object storage, would serve a large number of users.

To make it simple, we need a low-overhead execution runtime, a fast scheduler, and high-performance remote storage.

```
While the developer has no control of where a stateless function runs… the benefits of colocating computation and data – a major design goal for prior systems like Hadoop, Spark and Dryad – have diminished.
```

The PyWren prototype: data and serialized code both in S3, that's it…

1. Beyond simple map: 
map + monolithic reduce (such as featurization, ETL, the featurization requires parallel processing, but the generated features are often small and fit on a single large machine)

2. MapReduce: 
To implement the BSP model, in addition to parallel task execution, we need to perform data shuffles across stages => S3

3. Parameter Servers: sing low-latency, high throughput key-value stores like Redis. Since coordination across functions happens only through the parameter server, such applications fit very well into the stateless function model.

.

## Encoding, Fast and Slow: Low-Latency Video Processing Using Thousands of Tiny Threads
Why?
Running a video pipeline takes hours. Can we achieve interactive, collaborative video editing by using massive parallelism?

Challenges:  the finer-grain the parallelism, the worse the compression efficiency. 视频流不能随意切(video codec: key frame(base) + interframe frame(delta)

"fast" tasks are suitable for parallelization, and "slow" tasks require sequential execution. This approach enables faster video encoding while maintaining low latency.

Standard video encoders struggle with parallelization because exploiting temporal correlations between video frames often requires sequential processing.

How? Explicit state-passing exposes the intermediate computation needed by the mid-stream. Then, rebase those keyframes.

