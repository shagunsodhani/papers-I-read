---
layout: post
title: Cyclical Learning Rates for Training Neural Networks
comments: True
excerpt: Reasoning Network (ReasoNet) uses reinforcement learning (RL) to decide how many times a document should be read.
tags: ['2017', 'Accelerated Training', 'Learning Rate', 'WACV 2017', AI, LR, WACV]
---

## Introduction

* Conventional wisdom says that when training neural networks, learning rate should monotonically decrease. This insight forms the basis of the different type of adaptive learning rates.

* Counter to this expected behaviour, the paper demonstrates that using a cyclical learning rate (CLR), varying between a minimum and a maximum value, helps to train the neural network faster without requiring fine-tuning of learning rate.

* The paper also provides a simple approach to estimate the lower and upper bound for CLR.

* [Link to the paper](https://arxiv.org/abs/1506.01186)

* [Link to the implementation](https://github.com/bckenstler/CLR)

## Intution

* Difficulty in minimizing the loss arises from saddle points and not from local minima. [[Ref]](http://www.jmlr.org/papers/volume12/duchi11a/duchi11a.pdf)

* Increasing the learning rate allows for rapid traversal of saddle points.

* Alternatively, the optimal learning rate is expected to be between bounds of CLR and thus the learning rate would always be close to the optimal learning rate.

## Parameter Estimation

* Cycle Length = Number of iterations till learning rate returns to the initial value = 2 * step_size

* step_size should be set to 2-10 times the number of iterations in an epoch.

* Estimating the CLR boundary values:

    * Run the model for several epochs while increasing the learning rate between the allowed low and high values.

    * Plot accuracy vs learning rate and note the learning rate values when the accuracy starts to fall.

    * This gives a good candidate value for upper and lower bound. Alternatively, the lower bound could be set to be 1/3 or 3/4 of the upper bound. But it is difficult to judge if the model has run for the sufficient number of epochs in the first place.

## Notes

* The idea in itself is very simple and straight-forward to add to any existing model which makes it very appealing. 
* The author has experimented with various architectures and datasets (from vision domain) and has reported faster training results.