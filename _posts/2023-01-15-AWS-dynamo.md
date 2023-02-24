---
layout: post
title: notes on Dynamo: Amazon's Highly Available Key-value Store
---



The paper also describes a bunch of Paxos related optimizations that make running lots and lots of Paxos state machines practical

downsides of layering SQL on top of a key-value store: "node-local data structures have [...] poor performance on complex SQL [...], because they were designed for [...] key-value access". 

checkout tikv.org/deep-dive/introduction/ which goes over building a transactional SQL layer on top of a consensus backed key-value store, similar to Spanner tablet model.
The use of the TrueTime API (bounded of time of day)to support externally consistent transactions without resorting to routing all reads to a single leader


https://twitter.com/penberg/status/1608758159124090881


https://www.dynamodbguide.com/what-is-dynamo-db