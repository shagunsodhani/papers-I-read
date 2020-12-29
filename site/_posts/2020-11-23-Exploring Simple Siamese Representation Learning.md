---
layout: post
title: Exploring Simple Siamese Representation Learning
comments: True
excerpt: 
tags: ['2020', 'Self Supervised', AI, CV, ImageNet, Siamese, SSL, Unsupervised]


---

## Introduction

* The paper shows that Siamese networks can be used for unsupervised learning with images without needing techniques like negative sample pairs, large batch training, or momentum encoders. The training mechanism is referred to as the SimSiam method.

* [Link to the paper](https://arxiv.org/abs/2011.10566)


## Method

* Given an input image *x*, create two augmented views *x1* and *x2*. 

* These views are processed by an encoder network *f*.

* One of the views (say *x1*) is processed by the encoder *f* as well as a predictor MLP *h* to obtain a projection *p1* ie *p1 = h(f(x1))*.

* The second view (*x2*) is processed only by the encoder *f* to obtain an encoding *z2* i.e., *z2 = f(x2)*.

* Negative cosine similarity is minimized between *p1* and *z2* with the catch that the resulting gradients are not used to update the encoder via *z2*. I.e., Loss = *D(p1, stopgrad(z2))* where *D* is the negative cosine similarity and *stopgrad* is an operation that stops the flow of gradients.

* In practice, both *p1, z2* and *p2, z1* pairs are used for computing the loss. ie  Loss = *0.5 \* (D(p1, stopgrad(z2)) + D(p2, stopgrad(z1)))*.

## Implementation Details

* Encoder uses batch norm in all the layers (including output) while projection MLP uses batch norm only in the hidden layers.

* SGD optimizer with learning rate as *0.05 \* batchsize / 256*, cosine learning rate decay schedule and SGD momentum = 0.9.

* Unsupervised pretraining on the ImageNet dataset followed by training a supervised linear classifier on the frozen representations. 

## Results

* Stop-gradient operation is necessary to avoid a degenerate solution. Without stop-gradient, the model maps all inputs to a constant *z*.

* If the projection layer is removed, the method does not work (because of the loss's symmetric nature). If the loss is also made asymmetric, the method still does not work without the projection layer. However, asymmetric loss + projection layer works.

* Keeping the projection layer fixed (i.e., not updating during training) avoids collapse but leads to poor validation performance.

* Training the projection layer with a constant learning rate works better in practice, likely because the projection layer needs to keep adapting before the encoder layer is sufficiently trained.

* The method works well across different batch sizes.

* Removing batch norm layers from all the layers in all the networks does not lead to collapse, though the model's performance degrades on the validation dataset. Adding batch norm to the hidden layers alone is sufficient.

* Adding batch norm to the encoder's output further improves the performance but adding batch norm to all the layers of all the networks makes the training unstable, with the loss oscillating.

* Overall, while batch norm helps to improve performance, it is not sufficient to avoid collapse.

* The setup does not collapse when the cross-entropy loss replaces the cosine loss.


## What is SimSiam solving?

* Given that the stop-gradient operation seems to be the critical ingredient for avoiding collapse, the paper hypothesizes that SimSiam is solving a different optimization problem.

* The hypothesis is that SimSiam is implementing an Expectation-Maximisation (EM) algorithm with two sets of variables and two underlying sub-problems.

* The paper performs several experiments to test this hypothesis. For example, they consider *k* SGD steps for the first problem before performing an update for the second problem, showing that the alternating optimization is a valid formulation, of which SimSiam is a particular case.

## Comparison to other methods

* SimSiam achieves the highest accuracy among SimCLR, MoCo, BYOL, and SwAV for training under 100 epochs. However, it lags behind other methods when trained longer.

* SimSiam's representations are transferable beyond the ImageNet tasks.

* Adding projection layer and stop-gradient operator to SimCLR does not improve its performance.