---
layout: post
title: The Tail at Scale
comments: True
excerpt: 
tags: ['2013', 'Distributed Systems', ACM, Engineering, Latency, Scale, Systems]

---

## Introduction

* The paper presents some causes for (temporary) high-latency episodes in large-scale online systems and techniques to mitigate their impact so that the tail of latency distribution remains short.

* [Link to the paper](https://research.google/pubs/pub40801/)

## Why does variability in response time exist

* Shared resources between processes on the same node

* Background processes (daemons) could use cause a momentary spike in resource usage.

* Processes running on different nodes may contend for global resources like shared file systems.

* Maintenance activities like disk compaction or garbage collection.

* Others like queueing, power limits, or energy management.

* In the case of large-scale systems, the component-level variability is further amplified. 

## Reducing Component Variability

* Use differentiated service classes to prioritize user requests over non-interactive requests.

* Reduce head-of-line blocking by breaking long-running requests into smaller requests.

* Synchronize maintenance jobs across nodes to minimize the window for high latency.

* Caching generally does not help to address tail latency.

## Adapting to Latency Variability

* Two categories of adaptation approaches

	* Within Request Short-Term Adaptations

		* These approaches are more relevant for services that perform many read queries on loosely consistent datasets.

		* Hedged Request

			* Send the request to multiple replicas, and once one of the replicas returns the result, cancel the other requests.

			* In practice, start by sending the request to only one replica. Send the secondary requests if the first request is outstanding for more than $95^{th}$ percentile of expected latency.

			* This introduces an additional $5\%$ load while substantially shortening the latency tail.

			* This approach work because often, the cause of latency is not the query itself but other factors like overloaded nodes.

		* Tied Request

			* Hedged request approach makes a tradeoff regarding how long to wait before initiating requests to other replicas. The sooner the request is made, the lower should be the latency in serving the request, but more will be the overall load in the system.

			* The load in the system can be reduced by "tieing" requests (sent to different replicas) so that as soon as one replica starts processing the request, it can notify the other replicas, which could drop the request or deprioritize it.

			* In practice, "tieing" requests means that each replica has the identity of other replicas which may execute the request.

			* Note that there is a short window (of the average network message delay) when multiple replicas could start executing the request. This can be mitigated if the client (issuing the requests) introduces a delay to twice the average network message delay.

		* Submit the request to the least loaded replica

			* This is less effective for reasons like the load on a replica can change after the request is made but before it is executed.


	* Cross-Request Long-Term Adaptations

		* These approaches are more relevant for situations where different services have different throughput.

		* Micro-partitions

			* Generate more paritions than the number of nodes.

			* The partitions can be dynamically assigned to machines to ensure proper load balancing.

			* In case of machine failure, many nodes can be used to quickly re-create the micro-partitions instead of waiting on one machine to read one single large partition.

		* Selective Replication

			* With micro-partitioning, replicas for micro-partitions can be created ahead of time to achieve good load balancing.

		* Latency induced probation

			* In some cases, removing a slow node can improve the overall latency of the system. The probated node can be re-incorporated when its latency improves.

* Large Information Retrieval Systems

	* In such systems, speed can be more critical than the quality of the result.

	* The system should return a "good enough" result that is available with low latency instead of waiting for the "best result" that is available with high latency.

	* In some cases, a request could trigger an unexpected code path or cause some other exception that could slow down the entire system.

	* In such cases, the *canary request* technique can be used where the system sends the request initially to only 1 or 2 nodes. The request is sent over to the other nodes only after receiving a successful response from the initial nodes.

* Requests that update state are easier to handle for several reasons:

	* The scale of latency-critical modifications is generally small.

	* The update can be performed asynchronously after responding to the user.

	* Quorum-based approaches (often used for ensuring consistent updates) are inherently tail-tolerant.