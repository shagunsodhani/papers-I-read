---
layout: post
title: One-shot Learning with Memory-Augmented Neural Networks
comments: True
excerpt: 
tags: ['2016', 'Memory Augmented Neural Network', 'Meta Learning', 'One shot learning', AI, MANN, Memory]
---

## Introduction

* The paper demonstrates that Memory Augmented Neural Networks (MANN) are suitable for one-shot learning by introducing a new method for accessing an external memory.

* This method focuses on memory content while earlier methods additionally used memory location based focusing mechanisms.

* Here, MANN refers to neural networks that have an external memory. This includes Neural Turning Machines (NTMs) and excludes LSTMs.

* [Link to the paper](https://arxiv.org/abs/1605.06065)

## Meta-Learning

* In meta-learning, a learner is learning at two levels.

* The learner is shown a sequence of tasks D<sub>1</sub>, D<sub>2</sub>, ..., D<sub>T</sub>.

* When it is training on one of the datasets (say D<sub>T</sub>), it learns to solve the current dataset.

* At the same time, the learner tries to incorporate knowledge about how task structure changes across different datasets (second level of learning).

## MANN + Meta Learning

* Following are the desirable characteristics for a scalable, combined architecture:

    * Memory representation should be both stable and element-wise accessible.

    * Number of model parameters should not be tied to the size of the memory.

## Task Setup

* In standard learning, the goal is to reduce error on some dataset D. In meta-learning, the goal is to reduce the error across a distribution of datasets p(D).

* Each dataset is presented to the model in the form (x<sub>1</sub>, null), (x<sub>1</sub>, y<sub>0</sub>), ..., (x<sub>t+1</sub>, y<sub>t</sub>) where y<sub>t</sub> is the correct label (or value) corresponding to the inpuit x<sub>t</sub>.

* Further, the data labels are shuffled from dataset to dataset.

* The model must learn to hold the data samples in memory till the appropriate candidate labels are presented in the next step.

* The idea is that a model that meta learns would learn to map data representation to correct labels regardless of the actual context of data representation or the label.

* The paper uses NTM as the MANN with one modification.

* In the original formulation, the memories were addressed by both context and location. Location-based addressing is not optimal for the current setup where information encoding is not independent of the sequence.

* A new access module - LRUA - Least Recent Used Access - is used to write to memory.

* LRUA is purely content-based and writes to either least used memory location (to preserve recent information) or most recently used memory location (to overwrite recent information with more relevant information). This is decided on the basis of interpolation between previous read weights and weights scaled according to the usage weight.

## Datasets

* Omniglot (classification)

* Sampled functions from Gaussian Processes

## Results

* For the omniglot dataset, the model was trained with various combinations of randomly chosen classes with randomly chosen labels.

* As baselines, following models were considered:

    * Regular NTM
    * LSTM
    * Feedforward RNN
    * Nearest Neighbour Classifier

* Since each episode (dataset created by the combination of classes) contains unique classes (with their own unique labels) it is important to clear the memory across different episodes.

* For the regression task, the data was generated from a GP prior with a fixed set of hyper-parameters which resulted in different functions.

* For both the tasks, the MANN architecture outperforms the LSTM architecture baseline NTMs.

