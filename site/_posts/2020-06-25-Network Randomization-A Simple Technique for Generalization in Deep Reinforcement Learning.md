---
layout: post
title: Network Randomization - A Simple Technique for Generalization in Deep Reinforcement Learning
comments: True
excerpt: 
tags: ['2019', 'Deep Reinforcement Learning', 'ICLR 2020', 'Reinforcement Learning', AI, DRL, Generalization, ICLR, RL]

---

## Introduction

* The paper proposed a Technique for improving the generalization ability of RL agents when evaluated on an unseen environment (which is similar to the training environment).

* [Link to the paper](https://openreview.net/forum?id=HJgcvJBFvB)

* [Link to the code](https://github.com/pokaxpoka/netrand)

## Approach

* The key idea is to learn features that are invariant across environments by using a randomized CNN (*f*) that randomly perturbs the inputs.

* The policy is trained using the randomized observations obtained using *f*.

* Invariant features are learned using a feature matching (FM) loss that matches the feature representation of the original and randomized observations.

* The random network's parameters are initialized as $\alpha I + (1 - \alpha) N(0, \sqrt\frac{2}{n_{in} + n_{out}})$ where $\alpha \in \[0, 1\]$, $N$ denotes the Gaussian Distribution and $n_{in}, n_{out}$ denote the number of input and output channels respectively.

* Xavier Normal distribution is used for randomization to maintain the variance between the input and the randomized input.

* *f* is randomized per iteration.

* During inference, the expected action is computed by approximating over *M* samples (i.e., randomizing the input *M* times).

## Environments

* 2D CoinRun, 3D DeepMind Lab, 3D Robotics Control Task

* The evaluation environments consist of different styles of backgrounds, objects, and floors.

## Baselines

* Regularization methods: Dropout, L2 regularization, Batch Normalization

* Dataset Augmentation methods: Cutout, Gray out, Inversion, Color Jitter

## Results

* On CoinRun, the proposed approaches significantly outperforms the other baselines during evaluation. The performance improvement saturates around 10 *M* samples.

* Cycle consistency is used to measure the similarity between two trajectories. The proposed method improves the cycle consistency as compared to the vanilla PPO baseline. It also produces sharper activation maps in the evaluation environments.

* For the large-scale experiments, when evaluated on 500 levels of CoinRun, the proposed method improves the success rates from 39.8% to 58.7%.

* On DeepMind Lab and Surreal robotics control tasks, the proposed method leads to agents that generalize better on the unseen environments (during evaluation). 
