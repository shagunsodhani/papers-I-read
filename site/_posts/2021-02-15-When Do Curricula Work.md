---
layout: post
title: When Do Curricula Work?
comments: True
excerpt: 
tags: ['2020', 'Curriculum Learning', 'ICLR 2021', AI, Empirical, ICLR]

---

## Introduction

* The paper systematically investigates when does curriculum learning help.

* [Link to the paper](https://arxiv.org/abs/2012.03107)

## Implicit Curricula

* Implicit curricula refers to the order in which a network learns data points when trained using stochastic gradient descent, with iid sampling of data.

* When training, let us say that the model makes a correct prediction for a given datapoint in the $i^{th}$ epoch (and correct prediction in all the subsequent epochs). The $i^{th}$ epoch is referred to as the *learned iteration* of the datapoint  (iteration in which the datapoint was learned).

* The paper studied multiple models (VGG, ResNet, WideResNet, DenseNet, and EfficientNet) with different optimizers (Adam and SGD with momentum).

* The resulting implicit curricula are broadly consistent within the model families, making the following discussion less dependent on the model architecture.

## Explicit Curricula

* When defining an explicit curriculum, three important components stand out.

### Scoring Function

* Maps a data point to a numerical score of *difficulty*.

* Choices:

	* Loss function for a model

	* *learned iteration*

	* Estimated c-score - It captures a given model's consistency to correctly predict a given datapoint's label when trained on an iid dataset (not containing the datapoint).

* The three scoring functions are computed for two models on the CIFAR dataset.

* The resulting six scores have a high Spearman Rank correlation. Hence for the rest of the discussion, only the c-score is used.

### Pacing Function

* This function, denoted by $g(t)$, controls the size of the training dataset at step $t$. 

* At step $t$, the model would be trained on the first $g(t)$ examples (as per the ordering).

* Choices: logarithmic, exponential, step, linear, quadratic, and root.

### Order

* Order in which the data points are picked:
	
	* *Curriculum* - Ordering points from lowest score to highest and training on the easiest data points first.

	* *Anti Curriculum* - Ordering points from highest score to lowest and training on the hardest data points first.

	* *Random* - Randomly selecting the data points to train on.


## Observations

* The paper performed a hyperparameter sweep over 180 pacing functions and three orderings for three random seeds over the CIFAR10 and CIFAR100 datasets. For both the datasets, the best performance is obtained with random ordering, indicating that curricula did not give any benefits.

* However, the curriculum is useful when the number of training iterations is small.

* It also helps with noisy data training (which is simulated by randomly permuting the labels).

* The observations for the smaller CIFAR10/100 dataset generalize to slightly larger datasets like FOOD101 and FOOD101N. 

