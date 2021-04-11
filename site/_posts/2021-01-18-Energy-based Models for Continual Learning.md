---
layout: post
title: Energy-based Models for Continual Learning
comments: True
excerpt: 
tags: ['2020', 'Catastrophic Forgetting', 'Continual Learning', 'Energy-Based Models', 'Lifelong Learning', 'Replay Buffer', AI, CL, EBM, LL]

---

## Introduction

* The paper proposes to use Energy-based Models (EBMs) for Continual Learning.

* In classification tasks, the standard approach uses a cross-entropy objective function along with a normalized probability distribution. 

* However, cross-entropy reduces all negative classes' likelihood when updating the model for a given sample, potentially leading to catastrophic forgetting.

* Classification can be seen as learning an EBM across separate classes. 

* During an update, the energy for a pair of samples and its ground truth class decreases while the energy corresponding to the pairs of sample and negative classes increases. 

* Unlike the cross-entropy loss, EBMs allow choosing the negative classes to update.

* [Link to the paper](https://arxiv.org/abs/2011.12216)


## Applications of EBMs for Continual Learning

* EBMs can be used for class-incremental learning without requiring a replay-buffer or generative model for replay.

* EBMs can be used for continual learning in setups without task boundaries, i.e., setups where the data distribution can change without a clear separation between tasks.

## EBMs

* Boltzman distribution is used to define the conditional likelihood of label $y$, given an input $x$. ie, $p(y\|x) = \frac{exp(E(x, y))}{Z(x)}$ where $Z(x) = \sum_{y \in Y}(-E(x, y))$. Here $E$ is the learnt energy function that maps an input-label pair to a scalar energy value.

* During training, the contrastive divergence loss is used.

* During inference, the class, for which the input-class pair has the least energy, is selected as the predicted class.

## EBMs for Continual Learning

### Selection of Negative Samples

* The paper considers several strategies for the selection of negative samples:
	
	* one negative class per sample. The negative class is sampled from the current batch of data. This selection approach performs best.

	* all the negative classes in a batch are used for creating the negative samples.

	* all the classes seen so far in training are used as the negative samples. This approach works the worst in practice.

* Given the flexibility of sampling the negative classes, EBMs can be used in the boundary-agnostic setups (where the data distribution can change smoothly without an explicit task boundary).

### Energy Network

* EBMs take both the sample and the class as the input. The class can be treated as an attention filter to select the most relevant information between the sample and the class.

* In theory, EBMs can train for any number of classes without knowing the number of classes beforehand. This is an advantage over the softmax-based approaches, where adding new classes requires changing the size of the softmax output layer.

### Inference

* During inference, all the classes seen so far are evaluated via the energy function. The class, which corresponds to the least energy sample-class pair, is returned as the selected class.


## Experiments

### Datasets

* Split MNIST

* Permuted MNIST

* CIFAR-10

* CIFAR-100


### Results in Boundary-Aware Setting


* The paper outperforms the standard continual learning approaches that neither uses a replay-buffer nor a generative model.

* Additionally, the paper shows that for the same number of parameters, the effective capacity of EMB models is higher than the effective capacity of standard classification models.

* The paper also shows that standard classification models tend to assign a high probability to new classes for both old and new data. EBMs assign the probability more uniformly (and correctly) across the classes.

* In an ablation study, the paper shows that both label conditioning and contrastive divergence loss help in improving the performance of EBMs.

### Results in Boundary-Agnostic Setting

* The performance gains in the boundary-agnostic setting are even more significant than the improvements in the boundary-aware setting.