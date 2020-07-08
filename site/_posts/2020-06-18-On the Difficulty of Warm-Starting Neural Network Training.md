---
layout: post
title: On the Difficulty of Warm-Starting Neural Network Training
comments: True
excerpt: 
tags: ['2019', 'Incremental Learning', 'Online Learning', 'Transfer Learning', AI, Empirical]

---

## Introduction

* The paper considers learning scenarios where the training data is available incrementally (and not at once).

* For example, in some applications, new data is available periodically (e.g., latest news articles come out every day).

* The paper highlights that, in such scenarios, the conventional wisdom of "warm start" does not apply. 

* When new data is available, it is better to train a new model from scratch than to update the model trained on previously available data.

* While the two setups lead to similar training performance, the randomly initialized model has a much better generalization performance.

* [Link to the paper](https://arxiv.org/abs/1910.08475)


## Basic Batch Updating

* Create two random, equally-sized partitions of the training data.

* Train the model till convergence on the first half of the data. Then train the model on the entire dataset.

* Models: ResNet18, MLPs, Logisitic Regression (LR)

* Dataset: CIFAR10, CIFAR100, SVHN

* Optimizers: Adam, SGD

* Warm starting hurts generalization in all the cases.

* The effect is more pronounced in the case of ResNets and MLPs (compared to LR) and harder CIFAR 10 dataset (as compared to SVHN dataset).

## Online Learning

### Passive Online Learning

* The model is given access to k new learning examples at each iteration.

* A warm started model reuses the previously initialized model and trains (till convergence) on the new batch of k items.

* A "randomly initialized" model is trained on all the examples (seen so far) from scratch.

* Dataset: CIFAR10

* Model: ResNet18

* As more training data becomes available, the generalization gap between the two setups increases, and warmup starts hurting generalization.

### Active Online Learning

* In this setup, the learner is trained to sample k new examples to add to the training dataset (using margin-based sampling).

* Like the previous setup, warmup strategy still hurts generalization.

## Transfer Learning

* Train a Resnet18 model on the CIFAR10 dataset and use this model to warm start training on the SVHN dataset.

* When a small percentage of the SVHN dataset is used, the setup resembles pretraining / transfer learning and performs better than training from scratch.

* As the percentage of the SVHN dataset increases, the warmup approach starts underperforming.

## Overcoming warm start problem

* ResNet18 model on CIFAR10 dataset

* When performing a hyper-parameter sweep over the learning rate and batch size, it is possible to train warm start models to reach the same generalization performance as training from scratch.

* Though, in that case, there are no computational savings as the warm-started models take about the same time (to converge) as the randomly initialized model.

* The increased training time indicates that the warm started model probably needs to forget the knowledge from previous training rounds.

* Warm start Resnet models, that generalize well, have a low correlation to their initialization stage (measured via Pearson correlation coefficient between the model weights).

* Generalization is damaged even when using a model trained on incomplete data for only a few epochs.

* For warm start models, the gradient (corresponding to the "new" data) is higher than that for randomly initialized models. This hints that regularisation may help to close the generalization gap. But in practice, regularization helps both the warmup and randomly initialized model.

* Warm starting only a few layers also does not close the gap.

* Adding some noise to the warm started model (with the motivation of having a partially random initialization) does help somewhat but also increases the training time.

* Motivating the problem as an instance of catastrophic forgetting, the authors use the EWC algorithm but report that using EWC hurts model performance.

* The paper does not propose a solution to the problem but provides a thorough analysis of the problem setup, which is quite useful for understanding the phenomenon itself.
