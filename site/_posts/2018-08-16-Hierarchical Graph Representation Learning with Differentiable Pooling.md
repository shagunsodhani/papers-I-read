---
layout: post
title: Hierarchical Graph Representation Learning with Differentiable Pooling
comments: True
excerpt: 
tags: ['2018', 'Graph Neural Network', 'Graph Representation', AI, Graph, GNN, Pooling]
---

## Introduction

* Most existing GNN (Graph Neural Network) methods are inherently flat and are unable to process the information in a hierarchical manner.

* The paper proposes a differentiable graph pooling operation, DIFFPOOL, that can generate hierarchical graph representations and can be easily plugged into many GNN architectures.

* [Link to the paper](https://arxiv.org/abs/1806.08804)

## Key Idea

* CNNs have spatial pooling operation that allows for deep CNN architectures to operate on coarse graph representations of input images.

* This notion cannot be applied as-is to graphs as they do not have a natural notion of spatial locality like images do.

* DIFFPOOL attempts to resolve this problem by learning a differentiable soft-assignment at each layer which is equivalent to pooling the cluster of nodes to obtain a sparse representation.

## Approach

* Given a graph *G(A, F)*, where *A* is the adjacency matrix and *F* is the feature matrix.

* Given a permutation invariant GNN that follows the message passing architecture. The output of this GNN can be expressed as *Z = GNN(A, X)* where *X* is the current feature matrix.

* Goal is to stack *L* GNN layers on top of each other such that the *l<sup>th</sup>* layer uses coarsened output from the  *(l-1)<sup>th</sup>* layer.

* This coarsening operation uses a cluster assignment matrix *S*.

* The learned cluster assignment matrix at layer *l* is denoted at *S<sup>l</sup>*

* Given *S<sup>l</sup>*, the embedding matrix for the *(l+1)<sup>th</sup>* layer is given as *transpose(S<sub>l</sub>)Z<sub>l</sub>* and adjancecy matrix is given by *transpose(S<sub>l</sub>)A<sub>l</sub>S<sub>l</sub>*

* A new GNN, called as GNN<sub>pool</sub> is used to produce the assignment matrix *S* by taking a softmax over *GNN<sub>pool</sub>(A<sup>l</sup>, X<sup>l</sup>)*

* As long as the GNN model is permutation invariant, the resulting DIFFPOOL model is also permutation invariant.

## Auxiliary Losses

* The paper uses 2 auxiliary losses to push the model away from spurious local minima early in the training.

* Link prediction objective - at each layer, link prediction loss ( = A - S(transpose(S))) is minimized with the intuition that the nearby nodes should be pooled together.

* Ideally, the cluster assignment for each node should be a one-hot vector so the entropy for cluster assignment per node is regularized.

## Baselines

* GNN based models
  * GraphSage
    * Mean pooling
    * Set2Set pooling
    * Sort pooling
  * Structure2vec
  * Edge conditioned filters in CNN
  * PatchySan
* Kernel based models
  * Graphlet, shortest path etc

## Model Variants

* GraphSage
  * Mean pool + Diff pool (3 or 2 layers)
* Structure2Vec + Diffpool
* Diffpool-Det
  * The assignment matrix *S* are generated using graph clustering algorithms.
* Diffpool-NoLP
  * The link prediction objective function is turned off.
* At each DiffPool layer, the number of classes is set to 25% of the number of nodes before the DiffPool layer.

## Results

* DiffPool obtains the highest average performance across all the pooling approaches and improves upon the base GraphSage architecture by an average of around 7%.

* In terms of runtime complexity, the paper reports that DiffPool does not incur any significant additional running time. But given that now there are 2 GNN models per layer, the size of the model should increase.

* DiffPool can capture hierarchical community structure even when trained on just the graph classification loss.

* One advantage of DiffPool is that the nodes are pooled in a non-uniform way so densely connected group of nodes would collapse into one cluster while sparsely connected nodes can retain their identity.