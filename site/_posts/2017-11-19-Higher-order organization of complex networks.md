---
layout: post
title: Higher-order organization of complex networks
comments: True
excerpt: The paper presents the concept of "network motifs" to understand the structural design of a network or a graph.
tags: ['2016', 'Science 2016', Clustering, Graph, Motif, Network,  Science]
---

## Introduction

* The paper presents a generalized framework for graph clustering (clusters of network motifs) on the basis of higher-order connectivity patterns.

* [Link to the paper](http://science.sciencemag.org/content/353/6295/163)

## Approach

* Given a [motif M](https://shagunsodhani.in/papers-I-read/Network-Motifs-Simple-Building-Blocks-of-Complex-Networks), the framework aims to find a cluster of the set of nodes S such that nodes of S participate in many instances of M and avoid cutting instances of M (that is only a subset of nodes in instances of M appears in S).

* Mathematically, the aim is to minimise the motif conductance metric given as *cut<sub>M</sub>(S, S') / min[vol<sub>M</sub>(S), vol<sub>M</sub>(S')]* where *S'* is complement of *S*, *cut<sub>M</sub>(S, S')* = number of instances of M which have atleast one node from both *S* and *S'* and *vol<sub>M</sub>(S)* = Number of nodes in instances of M that belong only to S.

* Solving the above equation is computationally infeasible and an approximate solution is proposed using eigenvalues and matrices.

* The approximate solution is easy to implement, efficient and guaranteed to find clusters that are at most a quadratic factor away from the optimal.

## Algorithm

* Given the network and motif M, form a motif adjacency matrix W<sub>M</sub> where W<sub>M</sub>(i, j) is the number of instances of M that contains i and j.

* Compute spectral ordering of the nodes from normalized motif laplacian matrix.

* Compute prefix set of spectral ordering with small motif conductance.

## Scalability

* Worst case *O(m<sup>1.5</sup>)*, based on experiments *O(m<sup>1.2</sup>)* where *m* is the number of edges.

## Advantages

* Applicable to directed, undirected and weighted graphs (allows for negative edge weights as well).

* In case the motif is not known beforehand, the framework can be used to compute significant motifs.

* The proposed framework unifies the two fundamental tools of network science (motif analysis and network partitioning) along with some worst-case guarantees for the approximations employed and can be extended to identify higher order modular organization of networks.

