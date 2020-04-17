---
layout: post
title:  "Distributed Systems, NoSQL & CAP Theorem"
date:   2020-04-18 00:25:35 +0530
categories: nosql
---

http://horicky.blogspot.com/2009/11/nosql-patterns.html

ACID:
1. Atomicity: When a transaction occurs, it should happen all at once or not at all. DB shouldn't be affected if the transaction is aborted.
2. Consistency: when a transaction is done, all db instances (replicas) should be in the same state
3. Isolation: Each transaction should be executed in isolation. Means transaction shouldn't cause race condition. Row locks to achieve that.
4. Durability: After transaction is committed, the modification should be persistent even if the db crashes.

SQL databases are not all acid compliant. The issue is with fully complying with the Isolation clause. MySQL in isolation level Read - Repeated is not 
Isolation Levels (https://stackoverflow.com/questions/4034976/difference-between-read-commit-and-repeatable-read):
1. Read Committed (RC): Guarantees that only committed data is seen in a read. Uncommitted data is not read. But if the same query is executed the second time in the same transaction, it makes no guarantee that the same data will be returned. The data might have been updated between the reads.
2. Repeatable read (RR): It ensures that on top of Read committed, that the read data will not be updated within a transaction. (But a new row might have been added and it will show that)
3. Serialisable (S): On top of above two guarantees, it guarantees that no new data can be seen by a subsequent read in the same transaction.
But serialisable is tough to achieve and wont allow concurrency.
MySQL defaults to RR while postgres defaults to RC

BASE:
1. Basic Availability: Favours availability over correctness/consistency.
2. Soft state: The db instances/replicas don't have to be consistent all the time.
3. Eventual Consistency: Eventually.

SQL databases can only vertically scale and not horizontally. While the rest of the system can horizontally scale, DB is a blocker achieving high scalability. So NoSQL came so that even the DB can scale horizontally. So now, we have multiple nodes and data is shared across all nodes. Now all data can be replicated in all nodes or it can be distributed and all data need not be present on all nodes. With distributing data over nodes, you need partition tolerance.

Cap theorem:
1. Given a system allows partition-tolerance, it can either be consistent or available
2. A database can only be 2 out of 3

Whats ACID, OLAP, OLTP
CAP (https://stackoverflow.com/questions/12346326/cap-theorem-availability-and-partition-tolerance?rq=1):
1. Consistency: data is same across clusters
2. Availability: Ability to access data even when a cluster goes down
3. Partition Tolerance: Cluster continues to function even if there is a partition (communication break) between the nodes. Say all nodes are up but cannot communicate.

CAP Combinations:
1. CA: data is consistent across all nodes as long as all nodes are online. And you can read/write from any node and be sure that data is same. but data wont resync even after partition is resolved. CA is not a valid option in case of distributed system.
2. CP: Data is consistent between all nodes and remains partition tolerant but would go unavailable in case of partition. MongoDB, Redis
3. AP: nodes will remain available and resync after the case of partition. But you cannot guarantee that all nodes have same data during partition. It ensures eventual consistency. It is implemented via hash rings. Eg: DynamoDB, Cassandra

Stated simply, you have the option to have a partition consistent or partition available database given it is distributed. 

CAP theorem does not cover all cases so PACELC is used for practical systems. The below image clarifies

This image helps to understand what lies where in PACELC graph.





- RDBMS are rather PA/PC than AC. AC is nothing.'
- Only single node systems can be CA


4 type of NoSQL databases:
1. key-value
2. document
3. graph
4. wide-column

Column oriented database can be useful for analytical systems. Can be a smart choice for data warehouse. Helps you retrieve the data from the columns of your choice and not all columns. Stores data physically in columnar way.


1. Redis: It is put under CP because redis cluster becomes unavailable in case of partition. Redis Sentinel & Redis cluster are two types of distributed redis setup. 
	1. Redis Sentinel: (AP). Sentinel manages failover. Helps set it up for high availability. 
	2. Redis Cluster: (CP). It is a multiple writer/ larger than ram solution. Basically a sharding solution. Does not provide perfect availability or consistency
	3. https://stackoverflow.com/questions/31143072/redis-sentinel-vs-clustering
	4. When use redis: https://stackoverflow.com/questions/10558465/memcached-vs-redis?rq=1
	5. Also read about lua for redis
2. Mongo: CP. In case of partition, Mongo does not accept writes and will only start accepting writes when partition is fixed or new master takes over
3. DynamoDB: AP
	1. https://stackoverflow.com/questions/20870041/amazon-dynamodb-strong-consistent-reads-are-they-latest-and-how
4. Cassandra AP: It uses consistent hashing to determine the nodes that should be responsible for handling given data. It provides lightwight transaction using PAXOS

Zookeeper is what you use to back something with strong consistency.

3 types of Consistency:
1. Eventual Consistency:
2. Strong Consistency: PAXOS
3. Strong Eventual Consistency:

https://stackoverflow.com/questions/29381442/eventual-consistency-vs-strong-eventual-consistency-vs-strong-consistency
diff between eventual consistency and eventual read consistency

Replication and sharding are 2 different things obviously.
1. Replication: All data mirrored across nodes. Normal master-slave.
2. Sharding: Data distributed among nodes. Every node contains some percentage of data. Also, every piece of data normally resides in 2 or more nodes. Now there are different systems which take care of writes in different ways. Every shard might have its own slave/replica.
https://dba.stackexchange.com/questions/52632/difference-between-sharding-and-replication-on-mongodb


Consensus: It is something needed in distributed system to decide whether to commit or abort by making each node agree on the decision.

Algorithms:
1. Paxos
2. Raft
3. Gossip Protocol

Overview article. not complete though: https://www.freecodecamp.org/news/a-thorough-introduction-to-distributed-systems-3b91562c9b3c/

Baap theoritical article: [https://www.preethikasireddy.com/post/lets-take-a-crack-at-understanding-distributed-consensus](https://www.preethikasireddy.com/post/lets-take-a-crack-at-understanding-distributed-consensus)
