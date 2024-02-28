---
layout: post
title: note on "Notes on GitLab Postgres Schema Design"
---

ref: https://shekhargulati.com/2022/07/08/my-notes-on-gitlabs-postgres-schema-design/


1. UUID and sorted UUID have it cost, choose right primary key type for a table, 
serial is not that bad.


2. use of internal and external id if the internal id is incremental.
expose primary id is both unsafe and poor user experience.


3. useing text type with check constraints
unlimited text with check vs varchar(n)
The problem with varchar(n) is that if n becomes more restrictive then it will require an exclusive lock. This can cause performance issues depending on the size of the table.


6. Foreign key constraints
1. performance issue
2. They don’t work well with online DDL schema migration operations especially in MySQL
3. It is difficult to maintain foreign key constraints once you shard your data into multiple database servers


7. Partitioning big tables
a. PARTITION BY RANGE
b. PARTITION BY LIST
c. PARTITION BY HASH

8. Supporting LIKE search use cases with Trigrams and gin_trgm_ops
//TODO GitLab uses GIN(Generalized Inverted Index) indexes to perform efficient searches.

9. Use of jsonb

use case
* Dump request data that will be processed later
* Support extra fields
* One To Many Relationship where many side will not have to its own identity
* Key Value use case
* Simpler EAV design