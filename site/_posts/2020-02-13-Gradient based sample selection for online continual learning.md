---
layout: post
title: Gradient based sample selection for online continual learning
comments: True
excerpt: 
tags: ['2019', 'Catastrophic Forgetting', 'Continual Learning', 'Lifelong Learning', 'NeurIPS 2019', 'Replay Buffer', AI, CL, LL, NeurIPS]

---

## Introduction

* Use of replay buffer (and rehearsal) is a common technique for mitigating catastrophic forgetting.

* The paper builds on this idea but focuses on the sample selection aspect ie, which data points to store in the replay buffer.

* It formulates sample selection as a constraint minimization problem and shows that the proposed formulation is equivalent to maximizing the diversity of the samples with respect to parameter gradient.

* [Link to the paper](https://arxiv.org/abs/1903.08671)

## Setup

* Supervised learning tasks

* Online stream of data (i.e., one or few datapoints accessed at a time).

* When considering the $t^{th}$ task, the objective is: minimize the loss on the current task without increasing the loss on any of the previous tasks.

* The above constraint can be rephrased as $dot(g_t, g_i) \gt 0 \forall i \in \[0, t-1\]$ where $g_t$ is the gradient for the $t^{th}$ task.

* This is equivalent to saying that the current task gradient should not interfere negatively with the previous task gradient.

## Approach

* In practice, the gradient constraint is enforced only over the examples in the minibatch (and not the full dataset). 

* The paper interprets the constraint satisfaction problem as approximating an optimal feasible region (in the gradient space) where current task performance can be improved without hurting the performance on the previous tasks.

* The approximate region (of the shape of a polyhedral convex cone) is determined using only the examples from the replay buffer. Hence, the optimal region (defined for the entire dataset) would be contained within the approximate region. 

* The size of the approximate region can be measured in terms of the solid angle defined by the intersection between the approximate region and a unit sphere.

* The paper argues that the approximate region can be made smaller by reducing the angle between each pair of gradients.

* The set of points, satisfying the constraint, can be computed using the Integer Quadratic Programming (IQP).

* Given that the problem setup is online learning, using IDP for every new data point is not feasible.

* An in-exact, greedy alternative is suggested where a score is maintained for each example in the buffer.

* When a new datapoint comes in, the score is computed and used to decide if the existing datapoint in the buffer should be replaced.

* The score is the maximal cosine similarity of the current example with a random sample in the buffer.

## Results

* Benchmarks

    * Disjoint MNIST

    * Permuted MNIST

    * Disjoint CIFAR10

* Shared head setup 

* Baselines for sample selection

    * Randomly select examples to keep in the buffer.

    * Perform clustering - either in the feature space or in the gradient space.

    * Use IQP to select the examples. This approach is not used for CIFAR10, as it is computationally costly.

    * It would be interesting if the paper had considered baselines like selecting samples which had the largest loss.

* The proposed greedy approach outperforms the other methods.

* In an ablation experiment, the paper shows that the proposed approach works better than reservoir sampling (when the underlying data distribution is imbalanced).

* Another experiment compares the proposed approach with [Gradient Episodic Memory](https://papers.nips.cc/paper/7225-gradient-episodic-memory-for-continual-learning.pdf) and [iCaRL](https://arxiv.org/abs/1611.07725). For Permuted and Disjoint MNIST, the different methods perform quite similar though the proposed approach performs better on Disjoint CIFAR10.

