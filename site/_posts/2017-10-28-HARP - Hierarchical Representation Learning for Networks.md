---
layout: post
title: HARP - Hierarchical Representation Learning for Networks
comments: True
excerpt: HARP is an architecture to learn low-dimensional node embeddings by compressing the input graph into smaller graphs.
tags: ['2017', 'Graph Representation', AI, Embedding, Graph, SOTA]
---

## Introduction

* HARP is an architecture to learn low-dimensional node embeddings by compressing the input graph into smaller graphs.

* [Link to the paper](https://arxiv.org/abs/1706.07845).

* Given a graph *G = (V, E)*, compute a series of successively smaller (coarse) graphs *G<sub>0</sub>, ..., G<sub>L</sub>*. Learn the node representations in *G<sub>L</sub>* and successively refine the embeddings for larger graphs in the series.

* The architecture is independent of the algorithms used to embed the nodes or to refine the node representations.

* **Graph coarsening technique that preserves global structure**

    * Collapse edges and stars to preserve first and second order proximity.

    * **Edge collapsing** - select the subset of *E* such that no two edges are incident on the same vertex and merge their nodes into a single node and merge their edges as well.

    * **Star collapsing** - given star structure, collapse the pairs of neighboring nodes (of the central node).

    * In practice, first apply star collapsing, followed by edge collapsing.

* **Extending node representation from coarse graph to finer graph**
    
    * Lets say *node1* and *node2* were merged into *node12* during coarsening. First copy the representation of *node12* into *node1*, *node2*.

    * Additionally, if hierarchical softmax was used, extend the B-tree such that *node12* is replaced by 2 child nodes *node1* and *node2*.

    * Time complexity for HARP + DeepWalk is *O(number of walks \* \|V\|)* while for HARP + LINE is *O(number of iterations \* \|E\|)*.

    * The asymptotic complexity remains the same as the HARP-less version for the two cases.

* Multilabel classification task shows that HAR improves all the node embedding technique with gains up to 14%.