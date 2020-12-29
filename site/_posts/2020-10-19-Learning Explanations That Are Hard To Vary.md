---
layout: post
title: Learning Explanations That Are Hard To Vary
comments: True
excerpt: 
tags: ['2020', AI, Invariance]

---

## Introduction

* The paper builds on the principle "good explanations are hard to vary" to propose that *invariant mechanisms* can be identified by finding explanations (say model parameters) that are hard to vary across examples.
* [Link to the paper](https://arxiv.org/abs/2009.00329)
* [Link to the code](https://github.com/gibipara92/learning-explanations-hard-to-vary)

## Setup

* Collection of *d* different datasets (from different environments). Each dataset is a collection of input-target tuples.
* Objective is to learn a function *f* (also called *mechanism*) to map the input to the target (for all the environments).
* The standard approach is to pool the loss for examples corresponding to the different environments and perform gradient updates on this average-pooled loss.
* In this standard gradient-based setup, the model may not learn invariances due to the following reasons:
    * Model learned the spurious features first, and now the training loss is too small.
    * The pooled loss is generally computed by summing (or averaging) the loss corresponding to individual examples. Thus the gradient for each example is calculated independently. Each sample can be thought of as a dataset of size 1, for which all the features are relevant.
    * Gradient descent with averaging (of gradients across the environments) greedily maximizes for the learning speed and not invariance.
* Performing arithmetic mean can be seen as performing an OR operation (i.e., the sum can be high if any one of the constituents is high), whereas performing geometric mean can be seen as performing an AND operation (i.e., the product can be high only if all the constituents are high).

### Invariant Learning Consistency(ILC)

* Given an algorithm $$A$$, let $$\theta_{A}^{*}$$ denote the set of convergence points of $$A$$ when trained on all the environments.
* Each convergence point is associated with a consistency score.
* Intuitively, given a convergence point and an environment *e*, find the set of parameters equivalent to the convergence point (in terms of loss) with respect to *e*. Let's call this set as *S*.
* Evaluate the points in this set for all the remaining environments. For the given convergence point, an environment *e'* is consistent with *e* if the maximum difference in the loss for two environments is small, for all points belonging to *S*.
* This idea is used to define the invariant learning consistency score for algorithm $$A$$, which measures the expected consistency of the converged points (on the pooled data) across all the environments.
* The paper shows that the converged points' consistency is linked to the Hessians' geometric mean and that for the convex quadratic case, using the elementwise geometric mean of gradients improves consistency.
* However, there are some practical challenges:
    * Geometric mean is defined only when all signs are consistent. This issue can potentially be handled by treating different signs as 0.
    * There is very little flexibility in "partial" agreement, and even a single zero gradient component can stop optimization for that component. This can probably be handled by not masking if many environments have a gradient for that component.
    * Geometric component needs to be computed in the log-domain (for numerical scalability), but that can be computationally more expensive.
    * When using adaptive optimizers like Adam, the exact magnitude of geometric mean will be ignored because of rescaling for the local curvature adaptation.
* Some of these challenges can be handled using average gradients when the geometric mean would be 0 and masking out components based on the sign.

### AND-mask

* The ideas from the previous section can be used to develop a practical algorithm called AND-mask.
* Zero-out gradients that have inconsistent signs across some threshold number (hyper-parameter) of environments.
* In the presence of purely random gradient patterns, the AND-mask decreases the signals' strength exponentially fast.

## Experiments

### Synthetic Memorization Dataset

* This is a binary classification task with two kind of features: (i) "meaningful" features that are shared across environments but harder for the model to learn and (ii) "shortcut" features that are easy to learn but not shared across environments.
* While the dataset may look simple, it is difficult to find the invariant mechanism because the "shortcut" features allow for a simple, linear decision boundary, with a large margin that is fast to learn, has perfect accuracy, robust to input noise, and no iid generalization gap.
* Baselines:
    * MLPs trained with regularizers like dropout, L1, L2, and batch norm.
    * Domain Adversarial Neural Networks (DANN)
    * Invariant Risk Minimization (IRM)
* In terms of results, AND-mask with L1/L2 regularizers gives the best results.
* Empirically, the paper shows that the signal from the "meaningful" features is present when the gradients are averaged, but their magnitude is much smaller than the signal from the "shortcut" features.

### Experiments on CIFAR-10

* A ResNet model is trained on the CIFAR-10 dataset with random labels, with and without the AND-mask.
* The model with the AND-mask did not memorize the data, whereas the model without the AND-mask did. As sanity, the paper ensured that both the models generalize well when trained with the original labels.
* Note that for this experiment, every example was treated to have come from its own environment.

### Behavioral Cloning on CoinRun

* Train an expert policy using PPO for 400M steps on the full distribution of levels.
* Generate a dataset of state-action pairs. Training data consists of 1000 states from each of the 64 levels, while the test data comes from 2000 levels.
* A ResNet18 model is used as an imitation learning policy.
* The exact implementation of the AND-mask is a little more involved, but the key takeaway is that model trained with AND-mask identifies invariant mechanisms across different levels.