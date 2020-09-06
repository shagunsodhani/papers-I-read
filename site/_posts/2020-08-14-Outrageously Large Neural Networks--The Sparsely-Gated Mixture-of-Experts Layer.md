---
layout: post
title: Outrageously Large Neural Networks--The Sparsely-Gated Mixture-of-Experts Layer
comments: True
excerpt: 
tags: ['2017', 'Conditional Computation', 'Distributed Computing', 'ICLR 2017', 'Mixture of Experts', AI, Gating, ICLR]


---



## Introduction

* Conditional computation is a technique to increase a model's capacity (without a proportional increase in computation) by activating parts of the network on a per example basis.

* The paper describes (and address) the computational and algorithmic challenges in conditional computation. It introduces a sparsely-gated Mixture-of-Experts layer (MoE) with 1000s of feed-forward sub-networks.

* [Link to the paper](https://arxiv.org/abs/1701.06538)

## Practical Challenges

* GPUs are fast at matrix arithmetic but slow at branching.

* Large batch sizes amortizes the cost of updates. Conditional computation reduces the effective batch size for different components of the model.

* Network bandwidth can be a bottleneck with the network demand overshadowing the computational demand.

* Additional losses may be needed to achieve the desired level of sparsity.

* Conditional computation is most useful for large datasets.

## Architecture

* *n* Expert Networks - $E_1$, ..., $E_n$.

* Gating Network $G$ to select a sparse combination of experts.

* Output of the MoE module is the weighted sum of predictions of experts (weighted by the output of the gate).

* If the gating network's output is sparse, then some of the experts' value does not have to be computed.

* In theory, one could use a hierarchical mixture of experts where a mixture of experts is trained at each level.

### Choices for the Gating Network

* Softmax Gating

* Noisy top-k gating - Add tunable Gaussian noise to the output of softmax gating and retain only the top-k values. A second trainable weight matrix controls the amount of noise per component.

## Addressing Performance Challenge

* Shrinking Batch Problem

	* If the MoE selects *k* out of *n* experts, the effective batch size reduces by a factor of *k* / *n*.

	* This reduction in batch size is accounted for by combining data parallelism (for standard layers and gasting networks) and model parallelism (for experts in MoE). Thus, with *d* devices, the batch size changes by a factor of (*k* x *d* ) / *n*.

	* For hierarchical MoE, the primary gating network uses data parallelism while secondary MoEs use model parallelism.

	* The paper considers LSTM models where the MoE is applied once the previous layer has finished. This increases the batch size (for the current MoE layer) by a factor equal to the number of unrolling timesteps.

	* Network Bandwith limitations can be overcome by ensuring that the ratio of computation (of each expert) to the input and output size is greater than (or equal to) the ratio of computational to network capacity.

	* Computational efficiency can be improved by using larger hidden layers (or more hidden layers).

* Balancing Expert Utilization

	* Importance of an expert (relative to a batch of training examples) is defined as the batchwise sum of the expert's goal values.

	* An additional loss, called importance loss, is added to encourage the experts to have equal importance.

	* The importance loss is defined as the square of the coefficient of variation (of a set of importance values) multiplied by a (hand-tuned) scaling factor $w_{importance}$.

	* In practice, an additional loss called $L_{load}$ might be needed to ensure that the different experts get equal load (along with equal importance).

## Experiments

* Datasets

	* Billon Word Language modeling Benchmark

	* 100 Billion word Google News Corpus

	* Machine Translation datasets

		* Single Language Pairs - WMT'14 En to Fr (36M sentence pairs) and En to De (5M sentence pairs).

		* Multilingual Machine Translation - large combine dataset of twelve language pairs.

* In all the setups, the proposed MoE models achieve significantly better results than the baseline models, at a lower computational cost.
