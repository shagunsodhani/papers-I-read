---
layout: post
title: An Empirical Investigation of Catastrophic Forgetting in Gradient-Based Neural Networks
comments: True
excerpt: Neural Relational Inference for Interacting Systems
tags: ['2014', 'Catastrophic Forgetting', 'ICLR 2014' , AI, Activation, ICLR]
---

## Introduction

* *Catastrophic Forgetting* refers to the phenomenon where when a learning system is trained on two tasks in succession, it may forget how to perform the first task.

* The paper investigates this behaviour for different learning activations in presence and absence of dropout.

* [Link to the paper](https://arxiv.org/abs/1312.6211)

* [Link to the implementation](https://github.com/goodfeli/forgetting)

## Experiment Formulation

* For each experiment, two tasks are defined - "old" task and "new" task.

* The network is first trained on the "old" task until the validation set error has not improved for the last 100 epochs.

* The "best" performing model is then trained for the "new" task until the combined error on the "old" and the "new" validation datasets has not improved in the last 100 epochs.

* All the tasks used the same model architecture - 2 hidden layers followed by a softmax layer.

* Following activations were tested:
    * Sigmoid
    * ReLU
    * Hard Local Winner Takes It All
    * Maxout

* Models were trained using SGD with or without dropout.

* For each combination of the model, activation and the training mechanism, a random hyper param search was performed with set of 25 hyperparams.

* The authors took care to keep the hyperparams and other settings consistent and comparable across different experiments. Deviations, wherever applicable, and their reasons were documented.

## Observations

* In terms of the relationship between the "old" and the "new" tasks, three kinds of settings are considered:

    * The tasks are very very similar but the input is processed in a different format. For this setting, MNIST dataset was used with a different permutation of pixels for the "old" and the "new" task.

    * The tasks are similar but not exactly the same. For this setting, the task was to predict sentiments of reviews across 2 different product categories.

    * In the last setting, 2 dissimilar tasks were used. One task was to predict sentiment of reviews and another task was to perform classification over MNIST dataset (reduced to 2 classes).

* Using Dropout improved the overall validation performance for all the models for all the tasks.

* Using Dropout also increase the size of the optimal model across all the activations indicating that maybe the increased size of the model could explain the increased resistance to forgetting. It would have been interesting to check if dropout always selected the largest model possible given the set of the hyperparams.

* On the dissimilar task, dropout improved the performance while reducing the model size so it might have other properties as well that helps to prevent forgetting.

* As compared to the choice of training technique, the activation function has a less consistent effect on resistance to forgetting. The paper recommends performing cross-validation for the choice of the activation function. If that is not feasible, maxout activation function with dropout could be used.
