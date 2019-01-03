---
layout: post
title: Pre-training Graph Neural Networks with Kernels
comments: True
excerpt: 
tags: ['2018', 'Graph Neural Network', 'Graph Representation', AI, GNN, Graph, Kernel, Pretraining]
---

## Introduction

* The paper proposes a pretraining technique that can be used with the [GNN](https://shagunsodhani.in/papers-I-read/Neural-Message-Passing-for-Quantum-Chemistry) architecture for learning graph representation as induced by powerful graph kernels.

* [Paper](https://arxiv.org/abs/1811.06930)

## Idea

* Graph Kernel methods can learn powerful representations of the input graphs but the learned representation is implicit as the kernel function actually computes the dot product between the representations.

* GNNs are flexible and powerful in terms of the representations they can learn but they can easily overfit if a large amount of training data is not available as is commonly the case of graphs.

* Kernel methods can be used to learn an unsupervised graph representation that can be finetuned using the GNN architectures for the supervised tasks.

## Architecture

* Given a dataset of graphs *g<sub>1</sub>, g<sub>2</sub>, ..., g<sub>n</sub>*, use a relevant kernel function to compute *k(g<sub>i</sub>, g<sub>j</sub>)* for all pairs of graphs.

* A siamese network is used to encode the pair of graphs into representations *f(g<sub>i</sub>)* and *f(g<sub>j</sub>)* such that *dot(f(g<sub>i</sub>), f(g<sub>j</sub>))* equals *k(g<sub>i</sub>, g<sub>j</sub>)*.

* The function *f* is trained to learn the compressed representation of kernel's feature space.

## Experiments

### Datasets

* Biological node-labeled graphs representing chemical compounds - MUTAG, PTC, NCI1

### Baselines

* [DGCNN](https://www.cse.wustl.edu/~muhan/papers/AAAI_2018_DGCNN.pdf)
* Graphlet Kernel (GK)
* Random Walk Kernel
* Propogation Kernel
* Weisfeiler-Lehman subtree kernel (WL)

### Results

* Pretraining uses the WL kernel

* Pretrained model performs better than the baselines for 2 datasets but lags behind WL method (which was used for pretraining) for the NCI1 dataset.

## Notes

* The idea is straightforward and intuitive. In general, this kind of pretraining should help the downstream model. It would be interesting to try it on more datasets/kernels/GNNs so that more conclusive results can be obtained.
