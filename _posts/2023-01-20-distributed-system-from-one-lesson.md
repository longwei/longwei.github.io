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

