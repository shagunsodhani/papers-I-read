---
layout: post
title: Meta-Learning Update Rules for Unsupervised Representation Learning
comments: True
excerpt: 
tags: ['2018', 'ICLR 2019', 'Meta Learning', AI, ICLR, Unsupervised]
---

## Introduction

* Standard unsupervised learning aims to learn transferable features. The paper proposes to learn a transferable learning rule (in an unsupervised manner) that can generalize across tasks and architectures.

* [Paper](https://arxiv.org/abs/1804.00222)

## Approach

* Consider training the model with supervised learning - *&phi;<sub>t+1</sub> = SupervisedUpdate(&phi;<sub>t</sub>, x<sub>t</sub>, y<sub>t</sub>, &theta;)*. 

* Here *t* denotes the step, *(x, y)* denotes the data points, *&theta;* denotes the hyperparameters of the optimizer.

* Extending this formulation for meta-learning, one could say that *t* is the step of the inner loop, *&theta;* are the parameters of the meta learning model.

* Further, the paper proposes to use *&phi;<sub>t+1</sub> = UnsupervisedUpdate(&phi;<sub>t</sub>, x<sub>t</sub>, &theta;)* ie *y<sub>t</sub>* is not used (or even assumed to be available as this is unsupervised learning).

* The meta update rule is used to learn the weights of a meta-model by performing SGD on the sum of *MetaObjective* over the distribution of tasks (over the course of inner loop training).

## Model

* Base model: MLP with parameters *&phi;<sub>t</sub>*

* To ensure that it generalizes across architectures, the update rule is designed to be neural-local ie updates are a function of pre and postsynaptic neurons though, in practice, this constraint is relaxed to decorrelate neurons by using cross neural information.

* Each neuron *i* in every layer *l* (in the base model) has an update network (MLP) which takes as input the feedforward activations, feedback weights and error signals. ie *h<sub>b</sub><sup>l</sup>(i) = MLP(x<sub>b</sub><sup>l</sup>(i), z<sub>b</sub><sup>l</sup>(i), v<sup>l+1</sup>,
&delta;<sup>l</sup>(i), &theta;)*

    * *b* - index of the minibatch
    * *x<sup>l</sup>* - pre non-linearity activations
    * *z<sup>l</sup>* - post non-linearity activations
    * *v<sup>l</sup>* - feedback weights
    * *&delta;<sup>l</sup>* - error signal

* All the update networks share the meta parameters *&theta;*

* The model is run in a standard feed-forward manner and the update network (corresponding to each unit) is used to generate the error signal *&delta;<sup>l</sup><sub>b</sub>(i) = lin(h<sub>b</sub><sup>l</sup>(i))*.

* This loss is backpropogated using the set of learned backward weights *v<sup>l</sup>* instead of the forward weights *w<sub>l</sub>*.

* The weight update *&Delta;w<sub>l</sub>* is also generated using a per-neuron update network.

## Meta Objective

* The *MetaObjective* is based on fitting a linear regression model to labeled examples with a small number of data points.

* Given the emphasis on learning generalizable features, the weights (of linear regression) are estimated on one batch and evaluated on another batch.

* The *MetaObjective* is to reduce the cosine distance between *y<sub>b</sub>* and *v<sup>T</sup>x<sub>b</sub><sup>L</sup>*

    * *y<sub>b</sub>* - Actual lables on the evaluation batch

    * *x<sub>b</sub><sup>L</sup>* - Features of the evaluation batch (using the base model)

    * *v* - parameters of the linear regression model (learned on train batch)

## Practical Considerations

* Meta gradients are approximated using truncated backdrop through time.

* Increasing variation in the training dataset helps the meta optimization process. Data is augmented with shifts, rotations, and noise. Predicting these coefficients is an auxiliary (regression) task for training the meta-objective.

* Training the system requires a lot of resources - 8 days with 512 workers.

## Results

* With standard unsupervised learning, the performance (on transfer task) starts declining after some time even though the performance (on the unsupervised task) is improving. This suggests that the objective function for the two tasks starts to mismatch.

* *UnsupervisedUpdate* leads to a better generalization as compared to both VAE and supervised learning (followed by transfer).

* *UnsupervisedUpdate* also leads to a positive transfer across domains (vision to language) when trained for a shorter duration of time (to ensure that the meta-objective does not overfit).

* *UnsupervisedUpdate* also generalizes to larger model architectures and different activation functions.