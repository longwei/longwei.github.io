---
layout: post
title: notes on Distributed Systems in One Lesson by Tim Berglund
---


storage
single master storage (-consistency) =>read replica - (data schema，relation join)=> sharding


consistency => quorum, R + W > N

computation: mr => spark

scatter/gather paradigm
more general data model, RDD, DataSets
more general programming model, transform/action
storage agnostic


messaging, kafka
real-time analysis, compute on stream.
streams are also tables
means of loosely coupling subsystems
producers ==> subsribers by topics
persistent over the short term
event-broker push msg instead of pull


topic get too big? parition, and ordred in own parition
computer is not reliable enough
how strong we can guarantee?

now we got a message bus,
turn events into (rows-in-place) db, and then just compute events

lambda architecure
1. long-term storage, batch, slow but complete
2. tempeary queue, stream processing, fast summary
batch and stream code write twice, how about with streams?


time
centralized time or distributed time (if i know the max error, just wait)


4 levels of isolation
1. read uncommited, A can read uncommited write from B, even it haven't rollback.
2. read commited, A can read commited write from B during its own transcation. (the value should stay the same)
3. repeated read, A's read can't be changed by commited change from B no matter how many time it query. the value stay the same)
4. serializable, should the range query also isolated?

Optimistic vs. Pessimistic locking
optimistic lock: Compare-and-swap. Begin/Modify/Validate/Commit/Rollback
where you read a record, take note of a version number. and check that the version hasn't changed before you write the record back. 
When you write the record back you filter the update on the version to make sure it's atomic and update the version in one hit.
If the record is dirty (i.e. different version to yours) you abort the transaction and the user can re-start it.


Pessimistic: exclusive lock until transaction is done. distributed transcation using 2PC(XA protocol)
