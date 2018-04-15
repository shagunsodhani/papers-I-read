---
layout: post
title: Revisiting Semi-Supervised Learning with Graph Embeddings
comments: True
excerpt: The paper presents a semi-supervised learning framework for graphs where the node embeddings are used to jointly predict both the class labels and neighbourhood context.
tags: ['2016', 'ICML 2016', 'Graph Representation', AI, Embedding, Graph, ICML]
---

## Introduction

* The paper presents a semi-supervised learning framework for graphs where the node embeddings are used to jointly predict both the class labels and neighbourhood context. Usually, graph embeddings are learnt in an unsupervised manner and can not leverage the supervising signal coming from the labelled data.

* The framework is called [Planetoid (Predicting Labels And Neighbors with Embeddings Transductively Or Inductively from Data)](https://github.com/kimiyoung/planetoid).

* [Link to the paper](https://arxiv.org/abs/1603.08861)

## Problem Setting

* Given a graph G = (V, E) and x<sub>L</sub> and x<sub>U</sub> as feature vectors for labelled and unlabelled nodes and y<sub>L</sub> as labels for the labelled nodes, the problem is to learn a mapping (classifier) f: x -> y

* There are two settings possible:

    * **Transductive** - Predictions are made only for those nodes which are already observed in the graph at training time.

    * **Inductive** - Predictions are made for nodes whether they have been observed in the graph at training time or not.

## Approach

* The general semi-supervised learning loss would be *L<sub>S</sub> + &lambda;L<sub>U</sub>* where *L<sub>S</sub>* is the supervised learning loss while *L<sub>U</sub>* is the unsupervised learning loss.

* The unsupervised loss is a variant of the Skip-gram loss with negative edge sampling. 

* More specifically, first a random walk sequence S is sampled. Then either a positive edge is sampled from S (within a given context distance) or a negative edge is sampled.

* The label information is injected by using the label as a context and minimising the distance between the positive edges (edges where the nodes have the same label) and maximising the distance between the negative edges (edges where the nodes have different labels).

### Transductive Formulation

* Two separate fully connected networks are applied over the node features and node embeddings. 

* These 2 representations are then concatenated and fed to a softmax classifier to predict the class label.

### Inductive Formulation

* In the inductive setting, it is difficult to obtain the node embeddings at test time. One naive approach is to retrain the network to obtain the embeddings on the previously unobserved nodes but that is inefficient.

* The embeddings of node x are parameterized as a function of its input feature vector and is learnt by applying a fully connected neural network on the node feature vector.

* This provides a simple way to extend the original approach to the inductive setting.

## Results

* The proposed approach is evaluated in 3 settings (text classification, distantly supervised entity extraction and entity classification) and it consistently outperforms approaches that use just node features or node embeddings.

* The key takeaway is that the joint training in the semi-supervised setting has several benefits over the unsupervised setting and that using the graph context (in terms of node embeddings) is much more effective than using graph Laplacian-based regularization term.