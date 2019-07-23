---
layout: post
title: Set Transformer - A Framework for Attention-based Permutation-Invariant Neural Networks
comments: True
excerpt: 
tags: ['2018', 'ICML 2019', 'Relation Learning', 'Relational Learning', AI, ICML, Set]

---

## Introduction

* Consider problems where the input to the model is a set. In such problems (referred to as the set-input problems), the model should be invariant to the permutation of the data points.

* In "set pooling" methods ([1](https://arxiv.org/abs/1606.02185), [2](https://arxiv.org/abs/1703.06114)), each data point (in the input set) is encoded using a feed-forward network and the resulting set of encoded representations are pooled using the "sum" operator.

* This approach can be shown to be bot permutation-invariant and a universal function approximator.

* The paper proposes an attention-based network module, called as the Set Transformer, which can model the interactions between the elements of an input set while being permutation invariant.

* [Link to the paper](https://arxiv.org/abs/1810.00825)

## Transformer

* An attention function *Attn(Q, K, V) = (QK<sup>T</sup>)V* is used to map queries *Q* to output using key-value pairs *K, V*.

* In case of multi-head attention, the key, query, and value are projected into *h* different vectors and attention is applied on all these vectors. The output is a linear transformation of the concatenation of all the vectors.

## Set Transformer

* 3 modules are introduced: MAB, SAB and ISAB.

* Multihead Attention Block (MAB) is a module very similar to to the encoder in the Transformer, without the positional encoding and dropout.

* Set Attention Block (SAB) is a module that takes as input a set and performs self-attention between the elements of the set to produce another set of the same size ie *SAB(X) = MAB(X, X)*.

* The time complexity of the SAB operation is *O(n<sup>2</sup>)* where *n* is the number of elements in the set. It can be reduced to *O(m\*n)* by using Induced Set Attention Blocks (ISAB) with *m* induced point vectors (denoted as I).

* *ISAB<sub>m</sub> = MAB(X, MAB(I, X))*.

* ISAB can be seen as performing a low-rank projection of inputs.

* These modules can be used to model the interactions between data points in any given set.

## Pooling by Multihead Attention (PMA)

* Aggregation is performed by applying multi-head attention on a set of *k* seed vectors.

* The interaction between the *k* outputs (from PMA) can be modeled by applying another SAB. 

* Thus the entire network is a stack of SABs and ISABs. Both the modules are permutation invariant and so is any network obtained by stacking them. 

## Experiments

* Datasets include: 
    
    * Predicting the maximum value from a set.    
    * Counting unique (Omniglot) characters from an image.
    * Clustering with a mixture of Gaussians (synthetic points and CIFAR 100).
    * Set Anomaly detection (celebA).

* Generally, increasing *m* (the number of inducing datapoints) improve performance, to some extent. This is somewhat expected.

* The paper considers various ablations of the proposed approach (like disabling attention in the encoder or pooling layer) and shows that attention mechanism is needed during both the stages. 

* The work has two main benefits over prior work:

    * Reducing the *O(n<sup>2</sup>)* complexity to *O(m\*n)* complexity.

    * Using self-attention mechanism for both encodings the inputs and for aggregating the encoded representations.