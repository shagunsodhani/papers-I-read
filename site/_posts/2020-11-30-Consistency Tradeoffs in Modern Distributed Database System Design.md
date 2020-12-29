---
layout: post
title: Consistency Tradeoffs in Modern Distributed Database System Design
comments: True
excerpt: 
tags: ['2012', 'Big Data', 'Distributed Systems', 'Software Engineering', CAP, Data, Database, DBMS, Engineering, IEEE, Latency, Scale, Software, Systems]

---

## Introduction

* CAP theorem has been influential in the design decisions for distributed databases.

* However, designers incorrectly assume that the CAP theorem "always" imposes restrictions in terms of the tradeoff between availability and consistency. In contrast, the tradeoff is applicable only in the case of partitions.

* CAP theorem led to the development of highly available systems with reduced consistency models (and reduced ACID guarantees).

* Another tradeoff - between latency and consistency - has also been influential for database design.

* The paper unifies CAP and latency-consistency tradeoffs into a single formulation called PACELC.

* Note that some of the observations, especially ones about the databases, may be outdated now (the paper was written in 2012). However, the core message is still relevant.

* [Link to the paper](https://www.cs.umd.edu/~abadi/papers/abadi-pacelc.pdf)

## Latency-Consistency Tradeoff

* Low latency (or high availability) means that the system must replicate data.

* In case of an update query, three possibilities arise:

    * The system can choose to send data updates to all the replicas at once. This leads to two possibilities:

        * A replica can receive the update queries in an arbitrary order, thus breaking consistency with other replicas.

        * Alternatively, the replicas could use some protocol to agree on the order of updates. However, this can introduce latency.

    *  The update queries can be first sent to a master replica.

        * The master replica can apply the updates and send them to the other replicas using one of the following strategies:

            * Synchronous replication where the master waits for all the updates to be applied to a replica(s). However, this approach introduces latency.

            * Asynchronous replication where the master assumes the update to be complete before it completes. In this case, the latency-consistency tradeoff depends on how read queries are handled:

                * The system can send all read queries to the master. In this case, there are no consistency issues, but additional latency is introduced because all the read queries go to the same replica, thus potentially overloading it.

                * Alternatively, the read query can be served from any replica. While this improves read latency, the results can be inconsistent now.

            * Use a mix of Synchronous and Asynchronous replication - i.e., some of the write queries are Synchronous, and others are Asynchronous. In this case, the latency-consistency tradeoff depends on how read queries are handled:

                * If the read is routed to at least one replica that has been Synchrnously updated, the consistency can be preserved, with additional latency for discovering the updated replica, etc.

                * If the read query can not be routed to an updated replica (maybe because none of the replicas is updated), then either latency suffers or inconsistent read can be performed.

    * The update query is first sent to an arbitrary replica.

        * This is the same as the previous case, with the query going to an arbitrary replica instead of the master replica, and suffers from the same latency issues as the last case.

* In a nutshell, the tradeoff between latency and consistency  is always present, irrespective of network failure. 

* This contrasts with the CAP theorem, which imposes the tradeoff between availability and consistency only in the case of a network partition.

## PACELC

*  If there is a partition (P), how does the system tradeoff availability (A) and consistency (C); else (E), when the system is running without failures, how does the system tradeoff latency (L) and consistency (C)? 

* The latency-consistency tradeoff (ELC) is relevant only when the data is replicated. 

* Default versions of Dynamo, Cassandra, and Riak were PA/EL systems, i.e., if a partition occurs, availability is prioritized. In the absence of partition, lower latency is prioritized.

* Fully ACID systems (VoltDB, H-Store, and Megastore) and others like BigTable and HB are PC/EC, i.e., they prioritize consistency and give up availability and latency.

* MongoDB can be classified as a PA/EC system, while PNUTS is a PC/EL system.