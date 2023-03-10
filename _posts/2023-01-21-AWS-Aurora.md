---
layout: post
title: notes on CockroachDB
---

http://muratbuffalo.blogspot.com/2022/03/cockroachdb-resilient-geo-distributed.html


language layer + execuation layer + storage layer


write + mutiple read only endpoint across multiple AZ
===storage===data written here 6 times across AZs
AZ1  AZ2  AZ3


the trick is everything is decoupled, not locally persistent storage.
4 of 6 quorum write. 2 copy in one AZ, so even one AZ down, 

3 write still 4 copies for read guaranteed. 3 + 4 > 6


Scale: how to scale in single region?
add more instance on top of the shared storage and they all have immediate access to data

Resilience: if a read node failed, it can be recycled and all queries directed to others

BUT NOT FOR WRITE, as only single write Node. 
You can only scale up it, and when it failed, promote another read node.

transactional consistency
the isolation level is controlled within the write node,
it is default to READ_COMMITED(snapshot isolation) but it can set to SERIZALABLE.

 
 Now Aurora "Global" Database....

 write + mutiple read only endpoints across multiple AZ
===storage===data written here 6 times across AZs         <==ASYNC Repl=> secondary Aurora
AZ1  AZ2  AZ3

expands read scale into additional regions:
BUT, async replication only make data eventually consistent
signle write node globally doesn't solve the write latency.
resilience: now the secondary storage layer will promote to master, RPO over a min.

How to make sure the two regional is correct after the recovery? remediate it, pick one and overwrite another is expensive!

Compliance: HARD, how to deal with where the data lives!


Mutil-Master
a single region ONLY, two write endpoint only. NO read endpoint for both read and write.


