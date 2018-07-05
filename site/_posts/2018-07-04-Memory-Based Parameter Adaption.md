---
layout: post
title: Memory-based Parameter Adaptation
comments: True
excerpt: 
tags: ['2018', 'ICLR 2018', 'Continual Learning', 'Incremental Learning', 'Weight Adaptation', AI, ICLR]
---

## Introduction

* Standard Deep Learning networks are not suitable for continual learning setting as the change in the data distribution leads to catastrophic forgetting.

* The paper proposes Memory-based Parameter Adaptation (MbPA), a technique that augments a standard neural network with an episodic memory (containing examples from the previous tasks).

* This episodic memory allows for rapid acquisition of new knowledge (corresponding to the current task) while preserving performance on the previous tasks.

* [Link to the paper](https://arxiv.org/abs/1802.10542)

## Architecture

* MbPA consists of 3 components:

    * Embedding Network *f*
    * Memory *M*
    * Output network *g*

* *f* and *g* are parametric components while *M* is a non-parametric component.

* *M* is a dynamically sized dictionary where the key represents the output of the embedding network and the value represents the desired output for a given input (input to the model).

* When a new training tuple (x<sub>j</sub>, y<sub>j</sub>) is fed as input to the model, a key-value pair (h<sub>j</sub>, v<sub>j</sub>) is added to the memory. h<sub>j</sub> = f(x<sub>j</sub>)

* The memory has a fixed size and acts as a circular buffer. When it gets filled up, earlier examples are dropped.

* When accessing the memory using a key *h<sub>key</sub>*, the k-nearest neighbours (in terms of distance from the given key) are retrieved.

## Training Phase

* During the training phase, the memory is only used to store the input examples and does not interfere with the training procedure.

## Testing Phase

* During testing, the memory is used to adapt the parameters of the output network *g* while the embedding network *f* remains the same.

* Given the input x, obtain the embedding corresponding to x and using that as the key, retrieve the k-nearest neighbours from the memory.

* Each retrived neighbour is a tuple of the form (h<sub>k</sub>, v<sub>k</sub>, w<sub>k</sub>) where w<sub>k</sub> is propotional to the closeness between the input query and the key corresponding to the retrived example.

* The collection of all the retrieved examples are referred to as the context *C*.

* The parameters of the output network *g* are adapted from &theta; to &theta;<sub>x</sub> where &theta;<sub>x</sub> = &theta; + &delta;<sub>M</sub>(x, &theta;)

* &delta;<sub>M</sub>(x, &theta;) is referred to as the contextual update of parameters of the output network.

## Interpretation of MbPA

* MbPA can be interpreted as decreasing the weighted average of negative log likelihood over the retrieved neighbours in the context C.

* The expression corresponding to  &delta;<sub>M</sub>(x, &theta;) can be obtained by performing gradient descent to minimise the max a posterior over the context C.

* The a posterior expression can be written as a sum of two terms - one corresponding to a weighted likelihood of data in the context C and the other corresponding to a regularisation term to prevent overfitting the data.

* This idea can be thought of as a generalisation of attention. Attention can be viewed as fitting a constant function over the neighbourhood of memories while MbPA fits a more general function which is parameterised by the output network of the given model. Refer appendix E in the paper for further details.

## Experiments

* MbPA aims to solve the fundamental problem of enabling the model to deal with changes in data distribution.

* In that sense, it is evaluated on a wide range of settings: continual learning, incremental learning, unbalanced datasets and change in data distribution at test time.

* Continual Learning:
    
    * In this setting, the model encounters a sequence of tasks and cannot revisit a previous task.

    * Permuted MNIST dataset was used.

    * The key takeaway is that once a task is catastrophically forgotten, only a few gradient updates on a carefully selected data, are sufficient to recover the performance.

* Incremental Learning:
    
    * In this setting, the model is trained on a subset of classes and then introduced to novel, unseen classes. The model is tested to see if it can incorporate the new knowledge while retaining the knowledge about the previous classes.

    * Imagenet dataset with Resnet V1 model is used. It is first pretrained on 500 classes and then fine-tuned to see how quickly could it adapt to new classes.

* Unbalanced Dataset:
    
    * This setting is similar to the incremental learning setting with the key difference that once the model has been trained on a part of the dataset and is to be finetuned to acquire new knowledge, the dataset used for finetuning is much smaller than the initial dataset thus creating the effect of unbalanced datasets.

* Language Modelling: 
    
    * MbPA is used to adapt to the shift in the word distribution that is common to language modelling tasks. PTB and WikiText datasets were used. 

* MbPA exhibits strong performance on all these tasks showing that the memory-based parameter adaption technique is effective across a range of tasks in supervised learning. 