---
layout: post
title:  "System Design Approach"
date:   2020-10-29 00:25:35 +0530
categories: system-design
---
# Basic Concepts:

1. Communication within a cluster  (among the nodes in cluster):
	1. Tell everyone everything (not scalable O(n^2))
	2. Gossip Communication
	3. Distributed Cache
	4. Coordination Service: Leader Election
2. Leader Election (Zookeeper/ Kubernetes):
	1. Paxos 
	2. Raft
3. Rate Limiting: (All 5 explained quite well in https://www.youtube.com/watch?v=mhUQe4BKZXs) https://engineering.classdojo.com/blog/2015/02/06/rolling-rate-limiter/ 
	1. Token Bucket: Can be implemented with Redis.
	2. Leaky Bucket
	3. Fixed Window Counter
	4. Sliding Window Counter
	5. Sliding Window Log
4. Caching 
	1. Strategies
		1. Read Through
		2. Write Through
		3. Write Behind
		4. Refresh Ahead
	2. Eviction Policies:
		1. LRU
		2. LFU
		3. MRU
		4. FIFO
5. Load Balancing (Can be applied with Haproxy, Nginx): 
	1. Consistent Hashing
	2. Round Robin
6. Connection Types
	1. LongPoling
	2. WebSockets
	3. HTTP2
	4. Server Sent Events
7. Replication, Partitioning & Sharding
8. FLP Impossibility & CAP Theorem
9. Guarantees:
	1. 100% Delivery of message
  2. At least once
  3. At most once
  4. Exactly Once


# Specific Problems:
1. Search TypeAhead
2. Streaming DataTop K problem/ Heavy Hitters (Count Min sketch)
3. Check if exists: (BloomFilter)
4. Chat Service
5. LeaderBoard
6. File Storage
7. Instagram/FB/Twitter Feed
8. Ride Sharing

# How to approach a problem:

1. Capacity Estimation
2. List out all the component:
	1. Load Balancer
	2. Major microservices
	3. Persistent DB
	4. Cache
	5. Queues
	6. CDN
	7. Media storage/ S3
	8. Rate Limiter: which one
	9. Analytics, Aggregation, Log collection & tracing
3. Concepts to consider:
	1. RDBMS with sharding or NoSQL:  Table Structures
	2. What kind of cache: Caching Strategy
	3. What Kind of data structures in cache
	4. What kind of load balancer: (sticky or round-robin)
	5. Connection Types: TCP/HTTP/WebSocket/SSE
	6. Push vs Pull of data: Fanout
	7. Hot users/nodes/
4. Performance Optimisation
	1. consistent hashing for having in memory cache per node thus reducing network calls
	2. DB indexing, Primary & Secondary Indexes
	3. fast path, slow path approach for certain analytics and aggregation
	4. bloom filter & count min sketch approach

