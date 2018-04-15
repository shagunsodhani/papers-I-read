---
layout: post
title: Neural Message Passing for Quantum Chemistry
comments: True
excerpt: The paper presents a general message passing architecture called as Message Passing Neural Networks (MPNNs) that unify various existing models for performing supervised learning on molecules.
tags: ['2017', 'Message Passing', 'Neural Message Passing', 'Graph Representation', AI, Chemistry, Graph, MPNN]
---

# Introduction

* The paper presents a general message passing architecture called as Message Passing Neural Networks (MPNNs) that unify various existing models for performing supervised learning on molecules.

* Variants of the MPNN model achieve very good performance on the task of predicting the property of the molecules.

* [Link to the paper](https://arxiv.org/abs/1704.01212)

# MPNN

## Setting

* The input to the model is an undirected graph *G* where node features are represented as *x<sub>v</sub>* (corresponding to node *v*) and edge features are *e<sub>v, w</sub>* (corresponding to edge between nodes *v, w*).

* The idea is to learn a representation (or feature vector) for all the nodes (and possibly edges) in the graph and use that for the downstream supervised learning task.

* The model can be easily extended to the setting of directed graphs.

* The model works in 2 phases:

## Message Passing Phase

* All nodes send a *message* to their neighbouring nodes. The message is a function of the feature vectors corresponding to the sender node (or vertex), the receiver node and the edge connecting the two nodes. The feature vectors can be combined to form the message using the *message function* which can be implemented as a neural network.

* Once a node has received messages from all its neighbours, it updated its feature vector by aggregating all the message. The function used to aggregate and update the feature vector is called as the *update function* and can be implemented as a neural network.

* After updating the feature vectors, the graph could initiate another round of message passing. After a sufficient number of message passing rounds, the Readout phase is invoked.

## Readout Phase

* The feature vectors corresponding to different nodes in the graph are aggregated into a single feature vector (corresponding to the feature vector of the graph) using the *readout function*.

* The *readout function* can also be implemented using a neural network with the condition that it is invariant to the permutation of the nodes within the graph (to ensure that the MPNN is independent of the graph isomorphism).

# Existing Variants in literature

* The paper provides various examples where the existing architectures could be explained in terms of the message passing framework. This includes examples like [Convolutional Networks on Graphs for Learning Molecular Fingerprints](https://arxiv.org/abs/1509.09292), [
Gated Graph Sequence Neural Networks](https://arxiv.org/abs/1511.05493), [Graph Convolutional Networks](http://tkipf.github.io/graph-convolutional-networks/) etc.

# Experiments

## Setup

* Broadly speaking, the task is to predict the properties of given molecules (regression problem).

* The QM9 dataset consists of 130K molecules whose properties have been measured using Quantum Mechanical Simulations (DFT).

* Properties to be predicted include atomization energy, enthalpy, highest fundamental vibrational frequency etc.

* There are two benchmarks for error:

    * DFT Error - Estimated average error of DFT approximation

    * Chemical Accuracy - As established by the chemistry community

## Model 

* Following variants of *message function* are explored:

    * Matrix multiplication between *A<sub>evw</sub>* and *h<sub>v</sub>* where *A* is the adjacency matrix *h<sub>v</sub>* is the feature corresponding to node *v*.

    * Edge Network which is same as matrix multiplication case with the difference that *A* is a learned matrix for each edge type.

    * Pair Network where the feature vector corresponding to the source node, target node and edge is fed to a neural network.

## Virtual Elements

* Since all messages are shared via edges, it could take a long time for the message to move between two ends of the graph. To fasten this process, virtual elements are provided.

* In the first setting, "virtual edges" are inserted between nodes.

* In the second setting, a "master" node connects to all the other nodes.

## Message Passing Complexity

* In a graph with *n* nodes and *d* dimensional feature vectors, a single step of message passing would have the worst case time complexity of *O(n<sup>2</sup>d<sup>2</sup>*.

* This complexity can be reduced by breaking the *d* dimensional embedding into *k* different groups of *d/k* embeddings which can be updated in parallel. The complexity of the modified approach is *O(n<sup>2</sup>d<sup>2</sup>/k*.

# Results

* Best performing MPNN model uses edge network as the *message function* and [set2set](https://arxiv.org/abs/1511.06391) as the *readout function*.

* Using group of embeddings helps to improve generalization. This effect could also be because of ensemble-like nature of the modified architecture.

* The model performs worse without the virtual elements.

# Takeaways

* Long range interaction between vertices is necessary.

* Scaling to larger molecule sizes is challenging because the model creates a fully connected graph by incorporating virtual elements.