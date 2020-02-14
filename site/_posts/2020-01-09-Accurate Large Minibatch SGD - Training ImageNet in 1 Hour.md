---
layout: post
title: Accurate, Large Minibatch SGD - Training ImageNet in 1 Hour
comments: True
excerpt: 
tags: ['2017', 'Distributed Computing', 'Distributed SGD', 'Empirical Advice', 'Synchronous SGD', AI, ImageNet]

---


## Introduction

* Training models with large minibatches (using distributed synchronous SGD) can lead to optimization issues.

* The paper presents techniques for training models with large batch size while matching the accuracy of small minibatch setups.

* The paper focuses on the ImageNet dataset, but many of the proposed ideas are applicable broadly.

* [Link to the paper](https://arxiv.org/abs/1706.02677)

## Linear Scaling Rule

* When the minibatch size increases by a factor of *k*, the learning rate should also be increased by a factor of *k* (while keeping all other hyperparameters like weight decay fixed).

* Note that this is an empirical rule and is not expected to hold under all conditions.

* One such condition is when the model is changing rapidly during the first few epochs. In this case, a warmup phase is introduced to stabilize the model.

* The paper verifies that the scaling rule is applicable to batch sizes as large as 8K.

## Warmup

* The learning rate should be gradually ramped up from a small value to a large value to allow convergence.

## Batch Normalization

* Batch normalization uses batch statistics to normalize the data. Hence, the loss corresponding to each data point (in the batch) is not independent. Thus, changing the batch size could change the underlying function being optimized.

* In the distributed SGD setup, the per-GPU (or per-worker) batch size should be kept constant, and only one worker should compute the batch norm statistics.

## Pitfalls when using distributed SGD

* When using weight decay, scaling the cross-entropy loss is not the same as scaling the learning rate.

* When using momentum, changing the learning rate could require "momentum correction."

* Ensure that the per-worker loss is normalized by the size of the total minibatch and not just by the size of minibatch that each worker sees.

* For each epoch, uses a single random shuffling of the training data (before dividing between the workers).

## Communication

* The paper describes various techniques to speed up the training pipeline by reducing the communication overhead between nodes. (Each node can have one or more GPUs).

* First, a node sums the gradient from all the GPUs it has.

* The gradients are shared and summed across all the nodes.

* Each node broadcasts the resulting gradient to all the GPUs it has.

* Gradient Aggregation is performed in parallel with the backpropagation operator. While aggregating the gradient for one layer, the system starts computing the gradient of the next layer.

## Results

* Using these approaches, a Resnet50 model can be trained on the ImageNet dataset in an hour (using 256 workers).

* When an appropriate warmup strategy is used, the training and the validation curves (for the large batch size setup) matches the corresponding curves for the small batch size setup.

* The best performing warmup strategy is the one where training starts at a learning rate of 0.1 and linearly increases to 3.2 over five epochs.

* The paper shows that the results are not specific to the Resnet50 model (experiments with Resnet101 model) or the use case (experiments with object detection and instance segmentation using Mask R-CNN).

* Along with providing the empirical validation of the proposed ideas, the paper describes all the hyperparameters. It also includes the training and validation curves with the different configurations which enable others to replicate and build on this work.