---
layout: post
title: How transferable are features in deep neural networks
comments: True
excerpt: 
tags: ['2014', 'NIPS 2014', 'Transfer Learning', AI, NIPS, CV]
---

# Introduction

* When neural networks are trained on images, they tend to learn the same kind of features for the first layer (corresponding to Gabor filters or colour blobs). The first layer features are "general" irrespective of the task/optimizer etc.

* The final layer features tend to be "specific" in the sense that they strongly depend on the task.

* The paper studies the transition of generalization property across layers in the network. This could be useful in the domain of transfer learning where features are reused across tasks.

* [Link to the paper](http://papers.nips.cc/paper/5347-how-transferable-are-features-in-deep-neural-networks.pdf)

# Setup

* Degree of generality of a set of features, learned on task A, is defined as the extent to which these features can be used for another task B.

* Randomly split 1000 ImageNet classes into 2 groups (corresponding to tasks A and B). Each group has 500 classes and half the total number of examples.

* Two 8-layer convolutional networks are trained on the two datasets and labelled as baseA and baseB respectively.

* Now choose a layer numbered n from {1, 2...7}.

* For each layer n, train the following two networks:

    * **Selffer Network BnB**
        * Copy (and freeze) first n layers from baseB. The remaining layers are initialized randomly and trained on B.
        * This serves as the control group.

    * **Transfer Network AnB**
        * Copy (and freeze) first n layers from baseA. The remaining layers are initialized randomly and trained on B.
        * This corresponds to transferring features from A to B.

* If AnB performs well, n<sup>th</sup> layer features are "general".

* In another setting, the transferred layers are also fine-tuned (BnB<sup>+</sup> and AnB<sup>+</sup>).

* ImageNet dataset contains a hierarchy of classes which allow for creating the datasets A and B with high and low similarity.

# Observation

## Dataset A and B are similar

* For n = {1, 2}, the performance of the BnB model is same as baseB model. For n = {3, 4, 5, 6}, the performance of BnB model is worse. 

* This indicates the presence of "fragile co-adaption" features on successive layers where features interact with each other in a complex way and can not be easily separated across layers. This is more prominent across middle layers and less across the first and the last layers.

* For model AnB, the performance of baseB for n = {1, 2}. Beyond that, the performance begins to drop.

* Transfer learning of features followed by fine-tuning gives better results than training the network from scratch.

## Dataset A and B are dissimilar

* Effectiveness of feature transfer decreases as the two tasks become less similar.

## Random Weights

* Instead of using transferred weights in BnB and BnA, the first n layers were initialized randomly.

* The performance falls for layer 1 and 2. It further drops to near-random level for layers 3 and beyond. 

* Another interesting insight is that even for dissimilar tasks, transferring features is better than using random features.