---
layout: post
title: TaskNorm--Rethinking Batch Normalization for Meta-Learning
comments: True
excerpt: 
tags: ['2020', 'Batch Normalisation', ICML 2020', 'Meta Learning', AI, BatchNorm, BN, ICML, MAML, Normalization]


---



## Introduction

* Meta-learning techniques are shown to benefit from the use of deep neural networks.

* BatchNorm is a commonly used component when training deep networks, especially for vision tasks.

* However, BatchNorm and meta-learning make contradictory assumptions, and their combination may not work well in practice.

* The paper proposes TaskNorm, a normalization method that is designed explicitly for meta-learning.

* [Link to the paper](https://arxiv.org/abs/2003.03284)

## Setup

* Standard meta-learning setup with $k$ tasks, each task with its own context and target set.

* Two sets of parameters are considered during meta-learning - (i) global parameters, and (ii) task-specific parameters.

* Meta-learning setup can be viewed as an inference task, where the task-specific parameters are inferred using a context set and some additional (trainable) parameters.

* Normalization layers are commonly used to accelerate the training of neural networks. The general approach is to use normalization moments (statistics) along with some learned parameters.

* BatchNorm is a well-known and widely used normalization approach. It relies on the implicit assumption that the dataset comprises of iid samples from some underlying distribution.

* However, in meta-learning, data points are assumed to be iid only within a specific task.

* This leaves open the question of what moments to use during meta-train and meta-test time.

## Variants of BatchNorm

### Conventional BatchNorm (CBN)

* Compute moments at meta train time and use during meta test time.

* This is equivalent to lumping the moments with the global parameters. I.e., the running moments are shared globally, while the data is iid only locally.

* Using CBN with MAML leads to poor results.

* Moreover, meta-learning setup can some times require the use of a very small batch size. (e.g., 1-shot learning) In those cases, the computed statistics are likely to be inaccurate.

### Transductive BatchNorm (TBN)

* Use context/target set statistics at both meta-train and meta-test time.

* This is the default BatchNorm mode used in MAML.

### Instance-based normalization

* Moments are computed separately for each instance.

* This mode corresponds to treating the statistics as local at the observation level.

* These methods provide only limited improvement in performance, and can sometimes have a large overhead.

## Task Normalization (Proposed)

* The normalization statistics are local at the task level, and statistics for a given data point should only depend on the context set's data point. It should not depend on the other elements of the target set.

* Meta-Batch Normalisation (METABN) is a precursor to TaskNorm where the context set alone is used to compute the normalization statistics for both the context and the target set (during both meta-test and meta-train time).

* METABN does not perform well when used with small context sets.

* TaskNorm overcomes this limitation by using a set of non-transductive, secondary moments (computed from the input being normalized).

* When the context is small, using additional moments will help to improve the moment estimates.

* In the general case, a trainable blending factor, $\alpha$, is used to combine the two sets of moments.

* While the computational cost of TaskNorm is slightly more than CBN, it converges faster than CBN in practice.

* Normalization mechanism in Reptile can be interpreted as a particular case of TaskNorm.

## Experiments

* Small scale few-shot classification experiments

	* Omniglot and imin ImageNet dataset

	* First order MAML, with different kinds of normalization schemes.

	* Transductive BatchNorm performs the best.

	* Among non-transductive approaches, TaskNorm using Instance Normalisation augmentation performs the best.

	* Similar trend holds for the speed of convergence as well.

* Large scale few-shot classification experiments
	
	* MetaDataset dataset

	* CNAPs model

	* The context set's size varies across tasks in this setup and can be as small as 5.

	* TaskNorm with Instance Normalisation ranks first in 10 (out of 13) datasets and is also the fastest to train.

	* While Instance-based methods (Instance Normalisation and Layer Normalisation) are the slowest to converge, they still outperform the running average based methods (conventional BatchNorm).

	* The results demonstrate that designing meta-learning specific normalization methods can significantly improve performance and that Transductive BatchNorm may not always be the optimal choice.