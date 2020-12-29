---
layout: post
title: CAP twelve years later - How the rules have changed
comments: True
excerpt: 
tags: ['2012', 'Big Data', 'Distributed Systems', ACID, BASE, CAP, Database, DBMS, Engineering, IEEE, Latency, Scale, Systems]

---

## Introduction

* The CAP theorem states that any system sharing data over the network can only have at most two (out of three) desirable properties:

    * consistency (C), i.e., a single, up-to-date copy of the data;

    * high availability (A) of that data (for updates); and

    * tolerance to network partitions (P).

* This "2 of 3" formulation is misleading as it oversimplifies the interplay between properties.

* [Link to the paper](https://ieeexplore.ieee.org/abstract/document/6133253)

## ACID vs. BASE

* ACID is a design philosophy that focuses on consistency as reflected in the traditional relational databases.

* The four properties in ACID are:

    * Atomicity (A), i.e., the operations are atomic, and either the entire operation succeeds or none of it succeeds.
    
    * Consistency (C), i.e., a transaction preserves all the rules. Note that the consistency in CAP is a subset of consistency in ACID.
    
    * Isolation (I), i.e., transactions occur in isolation and do not affect each other.
    
    * Durability (D), i.e., the transactions are durable irrespective of system failure.


* BASE is an alternate design philosophy that focuses on availability as reflected in the NoSQL databases.

* The four properties in BASE are:

    * Basic Availability (BA), i.e., the database appears to work most of the time.

    * Soft state (S), i.e., the system's state can change over time as it becomes eventually consistent.

    * Eventual consistency (E), i.e., the system will eventually become consistent over time.

## CAP confusion

* Generally, partitionability is seen as a must-have, thus reducing the choice to be between availability and consistency.

* This view is somewhat misleading because the choice between C, A, and P is not binary but granular. 

* The choice between C and A can occur at various granularity levels, and different components (of a larger system) can prioritize different aspects.

* Similarly, the CAP theorem generally ignores latency even though it is closely related to partitionability. For example, failing to achieve consistency within a time-bound (i.e., latency) implies a partition.

* In general, there is no global notion of partition - some subset of nodes may experience a partition, and others may not.

* Once a partition is detected, the system can then choose between C and A.

## Managing Partitions

* Three-step process for managing partitions:

    * Detect the start of a partition.

    * Enter an explicit partition mode that may limit some operations.

        * Possible strategies:

            * Reduce availability by limiting some operations. 

            * Record extra information that can be used during partition recovery.

        * The strategy depends on the invariants that the system should maintain.

        * For example, if the invariant is that the keys (in a table) should be unique, the system could allow duplicate keys for some time and perform a de-duplication step during partition recovery.

        * A counterexample is a monetary transaction (e.g., charging a credit card). In such cases, the system could disable the operation and record it for performing later. Sometimes this "unavailability" is not visible to the user.

        * History of operations (over replicas across different partitions) can be tracked using version vectors of the form (node, logical time). The system can easily recreate the order in which they were executed (or mark them as being concurrent).

    * Initiate partition recovery when communication is restored and make the state across the partitions consistent.

    * One common approach is to revert to the state when the partition was detected and apply the operations consistently across all the replicas. 

    * This may require some extra effort to merge conflicts. 

    * One workaround can be to constrain the use of certain operations so that the system does not encounter merge conflicts during recovery.

    * Sometimes, certain invariants may be violated when the system is in the partition mode and needs to be fixed during recovery.

    * The key takeaway is that when partitions exist, the choice between availability and consistency is not binary, and both can be optimized for.