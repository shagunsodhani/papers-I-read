---
layout: post
title: Learned Optimizers that Scale and Generalize
comments: True
excerpt: 
tags: ['2017', 'ICML 2017', 'Hierarchial RNN', Learning Optimizer', 'Meta Learning', AI, ICML, Optimizer, RNN]
---

## Introduction

* The paper introduces a learned gradient descent optimizer that has low memory and computational overhead and that generalizes well to new tasks.

* [Link to the paper](https://arxiv.org/abs/1703.04813)

## Key Advantage

* Uses a hierarchial RNN architecture augmented by features like adapted input an output scaling, momentum etc.

* A meta-learning set of small diverse optimization tasks, with diverse loss landscapes is developed. The learnt optimizer generalizes to much more complex tasks and setups.

## Architecture

* A hierarchical RNN is designed to act as a learned optimizer. This RNN is the meta-learner and its parameters are shared across different tasks.

* The learned optimizer takes as input the gradient (and related metadata) for each parameter and outputs the update to the parameters.

* At the lowest level of hierarchical, a small "parameter RNN" ingests the gradient (and related metadata).

* One level up, an intermediate "Tensor RNN" incorporates information from a subset of Parameter RNNS (eg one Tensor RNN per layer of feedforward network).

* At the highest level is the glocal RNN which receives input from all the Tensor RNNs and can keep track of weight updates across the task.

* the input of each RNN is averaged and fed as input to the subsequent RNN and the output of each RNN is fed as bias to the previous RNN.

* In practice, the hidden states are fixed at 10, 30 and 20 respectively.

## Features inspired from existing optimizers

* Attention and Nesterov's momentum
    
    * Attention mechanism is incorporated by attending to new regions of the loss surface (which are an offset from previous parameter location).

    * To incorporate momentum on multiple timescales, the exponential moving average of the gradient at several timescales is also provided as input.

    * The average gradients are rescaled (as in RMSProp and Adam)

    * Relative log gradient magnitudes are also provided as input so that the optimizer can access how the gradient magnitude changes with time.
