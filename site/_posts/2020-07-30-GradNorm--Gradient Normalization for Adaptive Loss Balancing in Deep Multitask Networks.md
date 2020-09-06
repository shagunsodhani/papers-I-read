---
layout: post
title: GradNorm--Gradient Normalization for Adaptive Loss Balancing in Deep Multitask Networks
comments: True
excerpt: 
tags: ['2017', 'Gradient Manipulation', 'Gradient Normalization', 'ICML 2018', 'Multi Task', AI, ICML]


---



## Introduction


* The paper proposes GradNorm, a gradient normalization algorithm that improves multi-task training by dynamically tuning the magnitude of gradients corresponding to different tasks.

* [Link to the paper](https://arxiv.org/abs/1711.02257)

## Motivation

* During multi-task training, some tasks can dominate the training, at the expense of others.

* It is common to define the multi-task loss as a linearly weighted combination of the individual task losses.

* The paper proposes two changes to this setup:

	* Adapt weight-coefficients, assigned to each loss term, at each training step.

	* Directly modify the gradient magnitudes, corresponding to different tasks, so that all the tasks are learning at similar rates.

* Proposed GradNorm algorithm is similar to BatchNorm, but it performs normalization across tasks, not data batches.

## Algorithm

* Gradient norm at timestep $t$, for the $i^{th}$ task, is computed as the product between average gradient norm (across all tasks at timestep $t$) and $r_i(t) ^ {\alpha}$. 

* $r_i$ is the relative inverse training rate of task $i$. It is defined as the ratio between the loss ratio of task $i$ and the average loss ratio (across all the tasks).

* $\alpha$ is a hyperparameter.

* This computed per-task gradient norm is treated as the target value for actual gradient norms.

* An additional $L_1$ loss is incorporated between the actual and the target gradient norms, summed over all the tasks, and optimizes the weight-coefficients only.

* After every step, the weight-coefficients are renormalized to decouple the gradient normalization from the global learning rate.

* Note that all the gradient norm computations are performed only for the layers on which GradNorm is applied. Generally, GradNorm is used with only the last shared layer of weights (to save on computational costs).

## Experiments

* Two variants of NYUv2 dataset -- NYUv2+seg (small dataset) and NYUv2+kpts (big dataset).

* Both regression and classification setups were used.

* Models: 
	
	* SegNet with a symmetric VGG16 encoder/decoder

	* FCN with modified ResNet-50 as the encoder and shallow ResNet as the decoder.

* Standard pixel-wise losses for each task.

### Results

* GradNorm with $\alpha=1.5$ outperforms the equal-weight baseline and either surpasses or matches the best performance of single networks for each task.

* Almost any value of 0 < $\alpha$ < 3 improves the network's performance over an equal weight baseline. 
