---
layout: post
title: Network Motifs - Simple Building Blocks of Complex Networks
comments: True
excerpt: The paper presents the concept of "network motifs" to understand the structural design of a network or a graph.
tags: ['2002', 'Science 2002', Graph, Motif, Network,  Science]
---

## Introduction

* The paper presents the concept of "network motifs" to understand the structural design of a network or a graph.
* [Link to the paper](http://science.sciencemag.org/content/298/5594/824)

## Idea

* A network motif is defined as "a pattern of inter-connections occurring in complex networks in numbers that are significantly higher than those in randomized networks".

* In the practical setting, given an input network, we first create randomized networks which have same single node characteristics (like a number of incoming and outgoing edges) as the input network.

* The patterns that occur at a much higher frequency in the input graph (than the randomized graphs) are reported as motifs.

* More specifically, the patterns for which the probability of appearing in a randomized network an equal or more number of times than in the real network is lower than a cutoff value (say 0.01).

## Motivation

* Real-life networks exhibit properties like "small world" property ( the majority of nodes are within a distance of fewer than 7 hops from each other) and "scale-free" property (fraction of nodes having k edges decays as a power-law).

* Motifs are one such structural property that is exhibited by networks in biochemistry, neurobiology, ecology, and engineering. Further, motifs shared by graphs of different domains are different which hints at the usefulness of motifs as a fundamental structural property of the graph and relates to the process of evolution of the graph.
