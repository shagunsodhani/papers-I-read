---
layout: post
title: Design patterns for container-based distributed systems
comments: True
excerpt: 
tags: ['2016', 'Design Pattern', 'Distributed Systems', 'Software Engineering', Container, Engineering, Scale, Systems, USENIX]    

---

## Introduction

* The paper describes three design patterns for container-based distributed systems: single-container pattern, single-node pattern, and multi-node pattern.
* [Link to the paper](https://www.usenix.org/conference/hotcloud16/workshop-program/presentation/burns)

## Single-container management patterns

* Traditionally, containers have exposed three functions: run, pause and stop.
* A richer API can be implemented to provide fine-grained control to system developers and operators.
* Similarly, much more application information (including monitoring metrics) can be exposed.
* The container interface can be used to define a contract for a complex lifecycle. For example, instead of arbitrarily shutting down the container, the system could signal that it will be terminated, giving the container some time to perform some cleanup/follow-up actions.

## Single-node, multi-container pattern

### Sidecar pattern

* Multiple containers extend and enhance the main container.
* For example, a web-server serves from the local disk (main container) while a side container updates the data.
* Benefits:
    * independent development, deployment, and scaling of containers
    * possibility of combining different type of containers
    * failure containment boundary, i.e., one failing container, need not bring down the entire system.

### Ambassador pattern

* Proxy communication to and from the main container with the ambassador hiding the complexities of communication with a distributed (multi-shard system) that may be written in a different language.

### Adapter pattern

* Standardize output and interfaces across the containers to provide a simple, homogenized view to external applications.
* A common example is using a single tool for collecting/processing metrics from multiple applications.
* This is different from the adapter pattern, which aims to provide a simplified view of the external world to an application.

## Multi-node application patterns

### Leader election pattern

* In a sharded (or replication-based) system, the system may have to elect a leader (or multiple leaders) among the replicas (or shards).
* Instead of using a leader election library, a leader election container can be used (that communicates with other containers over, say, HTTP). This removes the restriction of using a leader election library compatible with the containers (e.g., using the same language).

### Work queue pattern

* A work coordinator container can queue different containers, each of which may have a different implementation or dependencies, thus removing the restriction that all the works use the same runtime.

### Scatter/gather pattern

* An external client sends a request to a root container.
* This container fans out the request to many containers that may perform the computation in parallel.
* The root container gathers these parallel computations' results and aggregates them into a response to the external client.