---
layout: post
title: Serverless Computing
---

TODO: Pre-Meetup Questions from CSE 291
Q: Today's serverless functions are stateless. How do you think different functions can share data and communicate?
A: lambda is stateless, so the state must be some other place, such as DB, Shared File/object, MessageQueue/event stream or HTTP API call chain.

Q: Can you think of any security threats of serverless computing? Bonus points if you can outline a real threat/attack.
A: risks inherent in multi-tenant resource sharing: data privacy , lib dependecy/run time, access control.
   solution:
   1. Scheduling randomization and physical isolation: deliberately preventing physical co-residency may conflict with placement to optimize start-up time, resource utilization, or communication.
   2. Fine-grained security contexts: access to Cloud functions need fine-grained configuration, including access to private keys, storage objects, and even local temporary resources(confused deputy)
   Example: Meltdown and Spectre

Q: Can you think of any other ways to reduce or avoid cold start for serverless computing (other than what the ATC'20 paper talks about).
A: 1. [Done. pre-warm] a pool of stand-by of warm VM and reroute the request to active instance that already bootup. 
   2. [ATC'20] dynamic policy on the keey-alive policy 
   3. [New] cached common library in the local machine to avoid code download time (need to standardlize code dependecies)
   4. [New] compress all files into one binary, save file IO overhead.
   5. [New] trim unused import, how to do it in dynamidc language?

One approach, reflected in AWS Lambda, is maintaining a “warm pool” of VM instances
that need only be assigned to a tenant, and an “active pool” of instances that have been used to
run a function before and are maintained to serve future invocation

------------------------


* slide

这是VM->Container->Func的进化

First Serverless app: BigQuery (AWS Athena)

AWS Lambda start handlers on worker("cold start") on RPC request and reuse the same container when possibile.

Good case: panallel, stateless tasks, orchstrating function
Bad case: stateful and distributed application (cooperation), access to storage


* Cloud Programming Simplified: A Berkeley View on Serverless Computing

Cloud computing relieved users of physical infrastructure management but left them with a proliferation of
virtual resources to manage.
two competing approaches. EC2 vs Google App Engine
EC2 win, but the downside of this choice was that developers had to manage virtual machines themselves,
basically either by becoming system administrators or by working with them to set up environments. 
(做过才知道多烦)

1. Redundancy for availability, so that a single machine failure doesn’t take down the service.
2. Geographic distribution of redundant copies to preserve the service in case of disaster.
3. Load balancing and request routing to efficiently utilize resources.
4. Autoscaling in response to changes in load to scale up or down the system.
5. Monitoring to make sure the service is still running well.
6. Logging to record messages needed for debugging or performance tuning.
7. System upgrades, including security patching.
8. Migration to new instances as they become available.


prerequisite: Decoupled computation and storage
serverless computing = Function aaS + Backend aaS
it must scale automatically with no need for explicit provisioning, and be billed based on usage. 

A common question when discussing serverless computing is how it relates to Kubernetes?
Kubernetes is a technology that simplifies management of serverful computing.

Serverless computing, on the other hand, introduces a paradigm shift that allows fully offloading
operational responsibilities to the provider.


* atc20
code start: code download => container start => runtime bootstrapped
warm start: code starts.

It is trade off of wasted memory vs cold start time.


profiling:
1. how function is accessed
2. what resource do they use
3. how long do function takes

80% qps is 1 per min.

apps are highligh heterogeneous

No one size fit all!

what's current provider do?
Fixed keep-alive 
AWS: keep 10 min (Azue 20 mins)

AWS: provisioned concurrancy

solution for fix is ofc adapt to each application (80% that is not use frequently) 

significant pattern? cron job or fixed keep-alive
too many OOB ITs? time series forcast (ARIMA)











