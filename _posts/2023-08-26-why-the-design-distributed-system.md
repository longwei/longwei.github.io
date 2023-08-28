---
layout: post
title: why the design distributed system
---

three-tier
front, app, db.

* easy and flexible
* state of the app is the weak spot



shard:

pro:
* build individual of complete chunk of the whole system
* pretend you are not building distributed system
* parition could cause two masters.
* client isolation is easy (GDPR)

con:
* complexity
* no comprehensive view of the data(ETL), monitoring and logging. pretend you are not building distributed system, but it is
* oversized shard (bad thing happend if a shared is too big)



lambda:
streaming or batch?, bounded vs unbounded 

assume:  unbouned, immutable data.
* long-term storage, batch, high latency
* temp queuing for unbounded, low latency
* write it to scale db. base frame + delta frame
* messaging. kafka. no global ordering, only within one partition.

com:
write the thing twice and only for analysis


streaming:
event first vs data frist
* integration is a first-classs concern
* Life is dynamic, DB are static

* storing data in message? rentention policy



SLA:
how to measure the healthy of the services:
Availability/Accurary/TPS/Latency

Consistency:
strong consistency, weak consistency and eventual consistency.

Data Durability:
Some form of replication is usually used to increase durability

Message Persistence and Durability

Idempotency
work for both side.

Designing for idempotent, distributed systems require some sort of distributed locking strategy.

both with distributed locks or database constraints
https://www.bennadel.com/blog/3390-considering-strategies-for-idempotency-without-distributed-locking-with-ben-darfler.htm?ref=blog.pragmaticengineer.com


Sharding and Quorum
sharding is an interesting area to learn more about, especially around resharding

The Actor Model VS CSP.



Operating a Large, Distributed System in a Reliable Way

Monitoring.

infra level monitoring. is this backend service healthy?
traffic, errors, latency(p99)
Business metrics monitoring


Oncall, Anomaly Detection & Alerting




Outages & Incident Management Processes

Runbooks
Communicating outages across the organization
Mitigation now, investigation tomorrow


Postmortems, Incident Reviews & a Culture of Ongoing Improvements
Postmortems
Incident reviews



Failover Drills, Capacity Planning & Blackbox Testing
Data center failovers are drills 


SLOs, SLAs & Reporting on Them

SRE as an Independent Team

Reliability as an Ongoing Investment














