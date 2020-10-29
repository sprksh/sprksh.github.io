---
layout: post
title:  "Different NoSQL Databases and when to Use Them"
date:   2020-10-29 00:25:35 +0530
categories: nosql
---

# General Format:
1. What type of database? Why?
2. When to use?
3. CAP Guarantees
4. Scale of Operation


# Redis 

Redis is a Key Value Store. It is basically a data structures store:
1. strings, 
2. hashes, 
3. lists, 
4. sets, 
5. sorted sets with range queries, 
6. bitmaps, 
7. hyperloglogs, 
8. geospatial indexes with radius queries and 
9. streams

1. Redis: It is put under CP because redis cluster becomes unavailable in case of partition. Redis Sentinel & Redis cluster are two types of distributed redis setup. 
	1. Redis Sentinel: (AP). Sentinel manages failover. Helps set it up for high availability. 
	2. Redis Cluster: (CP). It is a multiple writer/ larger than ram solution. Basically a sharding solution. Does not provide perfect availability or consistency
	3. https://stackoverflow.com/questions/31143072/redis-sentinel-vs-clustering
	4. When use redis: https://stackoverflow.com/questions/10558465/memcached-vs-redis?rq=1
2. Cassandra AP: It uses consistent hashing to determine the nodes that should be responsible for handling given data. It provides lightwight transaction using PAXOS

# DynamoDB

DynamoDB is KeyValue Store. Also called tabular data store.

CAP:
1. Availability over consistency. 
2. Option for Strong Consistency.(https://stackoverflow.com/questions/20870041/amazon-dynamodb-strong-consistent-reads-are-they-latest-and-how)
When to Use:
1. 

# Cassandra

Cassandra is Column Oriented/ Wide Column store.

CAP:
1. Availability over consistency.

When to use:
1. High Availability. Means it is not immediately Consistent. Rather eventual consistency.
2. Allows write to any node. Light weight transactions.
3. Favours writes over reads. Writes are faster. Means when writes are more than reads. Not good for analytical purposes. 
4. Good for targeted reads by primary key. Other queries sub-optimal.

# MongoDB

MongoDB is a document Sore.

CAP:
1. Consistent:  Mongo does not accept writes and will only start accepting writes when partition is fixed or new master takes over.

When to use:
1. When You have schema-less  data. Like from scraping web pages.

