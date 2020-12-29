---
layout: post
title: Data Management for Internet-Scale Single-Sign-On
comments: True
excerpt: 
tags: ['2006', 'Big Data', 'Distributed Systems', 'Key Value', 'Software Engineering', Data, Database, DBMS, Engineering, Scale, Software, SSO, Systems, USENIX]


---



## Introduction

* The paper describes the architecture of an erstwhile single-sign-on (SSO) service used by Google, called Google Accounts (2006).

* Note that some of the metrics and design decisions may be outdated now (the paper was written in 2006). However, the core message is still relevant.

* [Link to the paper](https://www.usenix.org/legacy/event/worlds06/tech/prelim_papers/perl/perl.pdf)

## Operational Constraints

* SSO's availability affects the availability of all applications that require user sign-in.

* Generally, systems can achieve high availability by sacrificing consistency, but given the nature of SSO (matching username/passwords), providing an inconsistent view is not a good option, and single-copy consistency is a usability requirement.

## Berkeley DB

* Berkeley DB is an embedded, high-performance, scalable, transactional storage system for key-value data and provides both keyed and sequential lookup.

* It provides a primary copy replication model with a single writer (called master) and multiple read-only replicas. 

* All writes are sent to the master, which first applies the changes and then propagates them to the replicas. 

* The master and the replicas have identical logs, and in case of master failure, a new master is elected from the replicas.

* Some synchronization may be needed between the replicas in case, e.g., the master dies in between a transaction.

## SSO Architecture

* SSO service maps usernames to user account data and services to service-specific data.

* The SSO database is partitioned into shards, where each shard is a replicated Berkeley DB (having 5 to 15 replicas).

* Each replica stores the data in a B+-link tree data structure.

* Consistent reads must go to the master, while non-master replicas can serve " stale" reads.

* In the case of larger replication groups (say 15 replicas), only a subset of replicas can become master ("electable replicas").

* In general, replicas are spread geographically to handle machine-failure, network-failure, and data center-failure. 

* Replicas in a share are kept close to reduce the communication latency, which affects the time to commit a write operation or electing a new master.

* Some of the shards implement ID-map, i.e., map of username to userid and userid to shards.

## Database Integration

* Berkeley DB leaves decisions regarding quorums, leases, etc., up to the application.

### Quorums

* SSO chooses a quorum protocol that guarantees that updates are never lost. 

* For the write queries, the master waits for a positive acknowledgment from a majority of the replicas, including itself, before marking the query as completed.

* When selecting a new leader, SSO requires a majority of replicas to agree. Moreover, Berkeley DB elections always choose a replica with the latest log entry during an election, thus guaranteeing that the new master's log will include all the previous master's updates.

### Leases

* The master holds a *master lease* when responding to read queries and refreshes this lease periodically by communicating with a majority of replicas.

* The lease guarantees that the master is not returning stale data if a partition or failure causes the master to lose its mastership, i.e., holding the lease guarantees that the master is still the master.

* Moreover, elections can not be completed within the lease timeout interval.

### Replica Group Membership


* SSO maintains a replica configuration containing the logical (DNS) name and IP address of each replica.

* In case of any changes to the configuration, the changes are specified in a file that the master reads periodically.

* If the configuration changes, the master initiates a configuration change and update the database. 

* Non-master replicas can get the new configuration from the database.

* A new replica or a replica that lost state (say due to a failure) starts as a non-voting replica and can not participate in an election till it has caught up with the master as of the time the replica joined (again).
