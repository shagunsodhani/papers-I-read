---
layout: post
title: Harvest, Yield, and Scalable Tolerant Systems
comments: True
excerpt: 
tags: ['1999', 'Distributed Systems', 'Operating Systems', CAP, OS, Scale]


---

## Introduction

* A classic paper that looks into strategies for scaling large systems that can tolerate graceful degradation.

* [Link to the paper](https://dl.acm.org/doi/10.5555/822076.822436)

## CAP Theorem

* CAP refers to strong **C**onsistency, high **A**vailability, and **P**artitionability.

* Strong consistency refers to single copy ACID consistency.

* High availability means any consumer can access the data anytime. Generally, this is achieved by adding one or more data replicas.

* Partitionability means that the system can survive a partition between the different replicas.

* Strong CAP theorem states that any system can have only two out of three properties.

* Weak CAP theorem says that stronger are the guarantees about any two properties, weaker are the third property's guarantees.

## Harvest, Yield, and CAP Theorem

* Assume that the clients are making a request to a server.

* There are two quantities of interest here:

    * Yield - the probability of completing a request.
    * Harvest - completeness of answer to a query.

* In the presence of faults, a tradeoff can is made between yield and harvest. This tradeoff applies to both read and update queries.

## Two strategies for scaling systems


### Trading Harvest for Yield

* In a hundred node cluster (without replication), a single-node failure reduces harvest by 1 %, and in the case of multi-node failure, the harvest degrades linearly.

* The probability of losing high-priority data can be reduced by replicating it. However, replicating all the data would not n guarantee 100% harvest and yield despite significant costs.

### Application Decomposition and Orthogonal Mechanisms

* Decompose a large application into subcomponents so that each component can be provisioned separately. Strong consistency can only be applied only on the components that need it, instead of the application as a whole.

* Further, failure of one or more components need not cause the application to fail as a whole. 

* Decomposition also provides the opportunity to use orthogonal mechanisms, i.e., mechanisms independent of other mechanisms with no runtime interface.

* Composition of orthogonal subsystems improves the robustness of runtime interactions by *locally* containing the errors. For example, the orthogonal components can be restarted /replaced independently without affecting other running components.