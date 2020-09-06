---
layout: post
title: Alpha Net--Adaptation with Composition in Classifier Space
comments: True
excerpt: 
tags: ['2020', 'Long-tailed Dataset', 'Transfer Learning', AI, Classifier, Compositionality]


---


## Introduction

* Common transfer learning method focuses on transferring knowledge in the model feature space.

* In contrast, the paper argues that the learned knowledge is more concisely captured in the "classifier space" as the classifier is fitted for all the samples for a given class, while the feature representation is specific to each sample.

* Building on this intuition, the paper proposes to combine strong classifiers (trained on large datasets) with weak classifiers (trained on smaller datasets) to improve the weak classifiers' performance.

* [Link to the paper](https://arxiv.org/abs/2008.07073)

## High-Level Idea

* Given $n$ classifiers, $C_1, ..., C_n$, trained with a large amount of data and a weak classifier $a$ trained for a class with few samples.

* Find the nearest neighbors of $a$.

* Train a new classifier by linearly combining $a$ with its nearest classifiers. 

* The coefficients (for linearly combining the classifiers) are learned using another classifier called as AlphaNet.

* In theory, this approach can be used with any set of classifiers.

## Setup

* A long-tailed dataset is one where some classes (referred to as the tail classes) have very few examples—for example, ImageNet-LT and Places-LT.

* Split the long-tailed dataset into two splits - "base" classes with $B$ (number of) classes and "few" classes with $F$ (number of) classes.

* Total number of classes $N = B + F$.

* Start with a pre-trained model, with classifiers $w_j$ and biases $b_j$ for $j \in (1, N)$.

* For a given target class $j$, find its top $k$ nearest neighbor classifiers and concatenate their output.

* For each "few" class, learn a feedforward network that takes the concatenated representation (of classifiers) as the input and returns a vector of $k \alpha$ values.

* These $\alpha$ values are interpreted as the classifier's strength (or confidence) in its nearest neighbors.

* The (normalized) alpha values are used for defining the weight and bias for the classifier for the given "few" class.

* The collection of all the "few" classifiers is referred to as the AlphaNet.

* The paper outlines a degenerate case, where the confidence in the prediction of all the strong classifiers goes to 0. The paper proposes to counter this case by clamping the $\alpha$ values.

* The entire setup is trained end-to-end using cross-entropy loss on AlphaNet.

## Results

* Given the proposed approach's flexibility, it is used to combine the state-of-the-art models on ImageNet-LT, namely retraining classifiers on class-balanced samples and training models with weight normalization. The combined setup outperforms the individual models.

* One interesting observation is that it is useful to include the weak classifiers, along with the strong classifiers, as AlphaNet adjusts the position of weak classifiers towards the appropriate strong classifier.

* While the idea is described in the context of long-tail data distribution, the idea is useful in the general context of non-stationary data distribution. One instantiation could be lifelong class incremental learning where the model encounters new data classes during training. For some time duration (till sufficient data points are seen), the newly seen classes are the "few" classes. This approach can help with faster adaptation when the model is yet to see sufficient examples for the unseen classes.