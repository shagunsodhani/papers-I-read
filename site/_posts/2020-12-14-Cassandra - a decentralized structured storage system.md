---
layout: post
title: Cassandra - a decentralized structured storage system
comments: True
excerpt: 
tags: ['2010', 'Big Data', 'Distributed Systems', 'Software Engineering', Apache, CAP, Data, Database, DBMS, Engineering, Scale, Software, Systems]    

---

## Introduction

* Cassandra is a distributed storage system that runs over cheap commodity servers and handles high write throughput while maintaining low latency for read operations.
* At the time of writing, it was used to support the search for Facebook Inbox.
* [Link to the paper](https://dl.acm.org/doi/10.1145/1773912.1773922)
* [Link to the implementation](https://cassandra.apache.org/)

## Data Model

* A table is a distributed multidimensional map.
* The key is a string (generally 16-36 bytes long), while the value is a structured object.
* Every operation under a single row key is atomic per replica.
* Columns are grouped together into sets called column families.
* There are two types of columns families:
    * Simple families.
    * Super column families: visualized as a column family within a column family.
* Columns can be sorted by name or time (used to display results in time sorted order).
* The API supports insert, get and delete operations.

## System Architecture

### Handling Requests

* Any read/write request gets routed to any node in the cluster. The node determines the replicas for a given key and routes the request.
* For write query, the system waits for a quorum of replicas to acknowledge the writes' completion.
* For read query, the system either routes the requests to the closest replica (might fetch stale results) or routes the requests to all replicas and waits for a quorum of responses.

### Partitioning

* Cassandra partitions data across the cluster using consistent hashing with an order-preserving hash function.
* The hash function's output range is treated as a fixed circular ring, and each node is assigned a random position on the ring.
* An incoming request specifies a key used to route requests.
* One benefit of this approach is that the addition/removal of a node only affects its immediate neighbors.
* However, randomly assigning nodes leads to non-uniform data and load distribution.
* Cassandra uses the load information and moves lightly loaded nodes to reduce the load on other nodes.

### Replication

* Each data item is replicated at N hosts, where N is the per-instance replication factor.
* Cassandra supports the following replication policies: Rack Unaware, Rack Aware (within a datacenter), and Datacenter Aware.
* For "Rack Aware" and "Datacenter Aware" strategies, Zookeeper elects a leader among the nodes and holds metadata about which range a node is responsible for.
* In case of node failure and network partitions, the quorum requirements are relaxed.

### Membership

* Cluster membership is based on Scuttlebutt, a very efficient anti-entropy Gossip based mechanism.
* Cassandra uses a modified version of $\phi$ Accrual Failure Detector for detecting failures, which provides the suspicion level (of failure) for each node.

### Bootstrapping

* A node, starting for the first time, chooses a random position in the ring.
* This information is persisted on the local disk, on Zookeeper, and gossiped around the cluster (so any node can route any query in the cluster).
* During bootstrapping, the newly joined node reads a list of contact points (within the cluster) using a configuration file.

### Local Persistence

* Generally, a write operation involves a write into a commit log (for durability and recoverability), followed by a write into the in-memory data structures.
* A read operation starts with querying the in-memory data and then looks into the filesystem.
* Read queries on the filesystem use bloom filters.
* Column indices are maintained to make it faster to look up relevant columns.

## Implementation Details

* Components implemented in Java.
* System control messages use UDP while messages for replication and request routing uses TCP.
* A new commit log is rolled out after the older one exceeds 128MB of size.
* All the data is indexed using a primary key.
* Data on the disk is chunked into sequences of blocks. Each block contains at most 128 keys and is demarcated by a block index.
* When the data is written to the disk, a block index is generated and maintained in the memory for faster access.
* A compaction process is performed to merge multiple files (on disk) into one file.

## Practical Experience

* Data from MySQL servers is added to Cassandra using MapReduce processes.
* Although Cassandra is a completely decentralized system, adding some coordination (via Zookeeper) is helpful.
* For Inbox Search, a per-user index is maintained for all the messages.
* For "term search", the key is the userid, and the words in the message become the super column.
* For searching all the messages ever sent/received by a user, the key is the userid, and the recipient ids are the super columns.