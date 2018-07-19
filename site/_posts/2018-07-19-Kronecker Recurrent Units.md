---
layout: post
title: Kronecker Recurrent Units
comments: True
excerpt: 
tags: ['2018', 'ICML 2018', 'Recurrent Neural Network', AI, ICML, Kronecker, KRU, NLP, RNN]
---

## Introduction

* Recurrent Neural Networks have two key issues:

  * **Over parameterization** which increases the time for training and inference.
  
  * **Ill conditioned** recurrent weight matrix which makes training difficult due to vanishing or exploding gradients.
    
* The paper presents a flexible RNN model called as KRU (Kronecker Recurrent Units) which overcomes the above problems by using a Kronecker factored recurrent matrix and soft unitary constraints on the factors.

* [Link to the paper](https://arxiv.org/abs/1705.10142)

## Related Work

### Existing solutions for overparameterization

* Low-rank decomposition.
  
* Training a neural network on the soft targets predicted by a big pre-trained network.
  
* Low-bit precision training.
  
* Hashing.

### Existing solutions for vanishing and exploding gradients

* Gating mechanism like in LSTMs.
  
* Gradient Clipping.
  
* Orthogonal Weight Initialization.

* Parameterizing recurrent weight matrix.

## KRU

* Uses a Kronecker factored recurrent matrix which enables controlling the number of parameters and number of factor matrices.

* Vanishing and exploding gradients are taken care of by using a soft unitary constraint.

* Why not use strict unitary constraint:
  
  * Restricts the search space and makes learning process unstable.
  
    * Makes forgetting (irrelevant) information difficult.
  
    * Relaxing the strict constraint has shown to improve the convergence speed and generalization performance.
    
* KRU can be easily plugged into RNNs, LSTMs and other variants.

* The recurrent matrix *W* is paramterized as a kronecker product of *F* matrices *W<sub>0</sub>, ..., W<sub>F-1</sub>* where each *W<sub>f</sub>* is a complex matrix of shape *P<sub>f</sub> x Q<sub>f</sub>* and the product of all *P<sub>f</sub>* and producto of all *Q<sub>f</sub>* are both equal to *N*.

* Why is *W* a complex matrix?
  
  * In the real space, the set of all unitary matrices have the determinant as 1 or -1.
  
    * Given that determinant is a continuous function, the unitary set in the real space is disconnected.
  
    * The unitary set in the complex space is connected as its determinants are points on the unit circle.

### Soft Unitary Constraint
  
* A soft unitary constraint is introduced in the form of regularization term ||W<sub>f</sub><sup>H</sup>W<sub>f</sub> - I ||<sup>2</sup> (per kronecker factored recurrent matrix).

* If each of the Kronecker factors is unitary, the resulting matrix *W* would also be unitary.

* It is computationally inefficient to apply this constraint over the recurrent matrix *W* itself as the complexity of the regularizer is given as *O(N<sup>3</sup>)*.

* Use of Kronecker factorisation makes it computationally feasible to use this regulariser.

## Experiment

* The Kronecker recurrent model is compared against the existing recurrent models for multiple tasks including copy memory, adding memory, pixel-by-pixel MNIST, char level language models, polyphonic music modelling, and framewise phoneme classification.

* For most of the task, KRU model produces results comparable to the best performing models despite using fewer parameters.

* Using soft unitary constraints in KRU provides a principled alternative to gradient clipping (a common heuristic to avoid exploding gradients).

* Further, recent theoretical results suggest the gradient descent converges to a global optimizer of linear recurrent networks even if the learning problem is non-convex provided that the spectral norm of the recurrent matrix is bound by 1.

* The key take away from the paper is that state should be high dimensional so that high capacity network can be used for encoding and decoding the input and output. The recurrent dynamics should be implemented via a low capacity model.s per task.