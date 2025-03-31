---
layout: post
title: element of distributed system
---

* bloom filter: no means no
* consistent hashing: dynamidc divide + vnode to even distribute workload
* quorum:  N/2+1, majority win even it is wrong.
* leader and follower: async replication and promotion. offset read load.
* WAL: append is better than random access. (segmented log)
* High-water mark: old leader's WAL may not sync. mark the last sync record, and only expose data under the mark to user.
* hearbeat and gossip protocol.
* Phi accurual Failure Detection: trade off between short timeout and long timeout
* lease: remediation for lock. time-bound contract, not monotonic increaseing number. resource management with a time-bound. preemptive 
* generation lock: split-brain? bump the term in leader election, so only the last leader matters
* fencing token: splt-brain? protecting external resource like DB from stale write. It is more frequently updated.
* vector clock
* PACELC theorem: what if no parition, choose latency or consistency
* hinted handoff: writes intended for an unavailable node are temporarily stored on another node and later forwarded when the target node becomes available. eventual delivery of writes to unavailable nodes. It does not change quorum rules—it just temporarily holds data
* sloppy quorum: relaxes quorum rules, allowing reads/writes to succeed on fallback nodes, even if the primary nodes are unavailable
* read repair: stale or inconsistent data is detected and fixed during read operations by comparing replicas and updating out-of-sync nodes. (maybe background repairs better?)
* merkle tree: store hashes at each node, enabling an efficient binary search-like approach to detect differences between datasets

