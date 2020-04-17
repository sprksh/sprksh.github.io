---
layout: post
title:  "Notes on PAXOS, RAFT & Understanding Partition Tolerant Replication in Distributed Systems"
date:   2020-04-18 00:25:35 +0530
categories: distributed-systems
---

Paxos and Raft both are leader based consensus protocols. Blockchain is leaderless consensus protocol.

Ensuring consistency of data among multiple nodes in when high availability is required:

1.  Consensus Protocols: Consensus-based replication has become key to building resilient, strongly consistent distributed systems. It wont give high availability as it wont be available in case of absence of a leader while the voting is happening.

1.  leader based
2.  leaderless

3.  Peer-to-peer/Gossip protocols: every replica of a shard is treated equal and hence can accept write requests. Meant for eventual/weak consistency.

However, a given distributed can be using both for different purposes. Like one for leader election and something else for failure detection. Like, Cassandra uses gossip for failure detection and metadata propagation but not actual replication.

![](https://www.evernote.com/shard/s737/res/eb757bb8-b571-4785-9302-21f9991fcfbb)

<https://blog.yugabyte.com/how-does-consensus-based-replication-work-in-distributed-databases/>

Gives a good overview.

Does Paxos/Raft

Byzantine failure: A Byzantine fault is a condition of a distributed computing systems, where components may fail and there is imperfect information on whether a component has failed.

**Consensus Algorithms:**

Paxos: A consensus algorithm.

In Paxos, it's important for everyone to agree to only allow one process to have write access about one piece of information at a given time.

The part time parliament. Everyone is in consensus of the latest law.

Each legislator maintained a ledgers of laws.

1.  Proposers: Those who accept the request and propose update
2.  Acceptors, voters
3.  Learners: Replicas

Majority is called quorum. Any two quorums must overlap

Only works for fail-stop problems. Doesn't take care of byzantine fault.

Raft: Another Consensus algorithm

<http://thesecretlivesofdata.com/raft/>

In each round, a leader would be chosen to propose new update.

A raft cluster has one and only one elected leader which communicates with client for updates. The leader is responsible for orchestrating the sending of message to other nodes

Leader Election:

1.  Leader sends heartbeat to nodes saying its alive.
2.  If nodes do not get heartbeat, they receive an election cycle

![](https://www.evernote.com/shard/s737/res/6bf76419-9018-475d-9923-3f28a4db3f13)

Raft is not Byzantine fault tolerant by default.

To reach consensus Raft needs the **majority** of nodes to be alive - > 50%.

This means in order to tolerate t failures, there have still to be t+1 nodes working correctly.

So 2t+1 nodes are needed to be **t-resilient**, which is the smallest amount of nodes needed to reach consensus in presence of **partial synchrony** [(3)](https://dl.acm.org/citation.cfm?id=42283) with just tolerating omission failures

Dynamo Paper:

No Consistency. So what? Not every database is a bank.

Resolve inconsistency later.: Last write wins.

Rather make a highly available distributed system.

Cassandra is inspired by original dynamoDB paper.

DynamoDB is different.

If you want to get in depth theoritically fron distributed systems,

<https://www.youtube.com/watch?v=7VbL89mKK3M&list=PLOE1GTZ5ouRPbpTnrZ3Wqjamfwn_Q5Y9A>