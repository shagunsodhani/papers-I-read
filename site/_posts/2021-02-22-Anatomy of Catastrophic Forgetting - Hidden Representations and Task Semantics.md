---
layout: post
title: Anatomy of Catastrophic Forgetting - Hidden Representations and Task Semantics
comments: True
excerpt: 
tags: ['2020', 'Catastrophic Forgetting', 'Continual Learning', 'ICLR 2021', 'Lifelong Learning', 'Replay Buffer', 'Representation Analysis', AI, CL, ICLR, LL]

---

## Introduction

* The paper studies the effect of catastrophic forgetting on representations in neural networks.

* [Link to the paper](https://arxiv.org/abs/2007.07400)

## Setup

* Techniques:

    * Representational Similarity Measures

    * Layer Freezing

    * Layer Reset

* Datasets

    * Split CIFAR-10

        * CIFAR-10 dataset is split into *m* (=2) tasks, where each task is a *n* way classification task.

        * The underlying network has a shared trunk with *m* heads, one head per task.

    * Split CIFAR-100 Distribution Shift

        * Each task requires distinguishing between *n* CIFAR-100 *superclasses* with training/test data corresponding to a *subset* of constituent classes.

* Network Architecture

    * VGG, ResNet and DenseNet

## Questions

* Are all representations (throughout the network) equally responsible for forgetting?

    * *Higher* layer (layers closer to the output) are the primary source of catastrophic forgetting.

    * [Central Kernel Alignment (CKA)](https://arxiv.org/abs/1905.00414) technique is used to compare the similarity between the layer representations, before and after training on the second task.

    * Higher layer representations change significantly when training over two tasks while the lower layer representations remain stable.

    * When finetuning on the second task, freezing the lower layers has only a minor effect on the accuracy of the second task.

    * In *layer reset* experiments, after training on the second task, the weights of some of the layers are reset to their values after training on the first task.

        * Resetting the weights of higher layers leads to significant improvement in the performance on the first task.

* Do common approaches for countering catastrophic forgetting work by stabilizing the higher layers?

    * Yes - both [EWC](https://arxiv.org/abs/1612.00796) and replay-based approaches counter catastrophic forgetting work by stabilizing the higher layers.

    * This is demonstrated by showing that as the quadratic penalty for EWC (or fraction of data from replay buffer) increases (to reduce catastrophic forgetting), the representations for higher layers change less during the second task.

* When training over a sequence of tasks, are similar tasks more likely to be forgotten than different tasks?

    * Setup I

        * Training over a sequence of two binary classification tasks.

        * Task 1: Two related classes (say `ship` and `truck`)

        * Task 2: Two related classes, which may or may not be related to the classes for Task 1. For example, the classes could be

            * `cat` and `horse` (not related to classes of the first task)

            * `plane` and `car` (related to the classes of the first task)
        
        * Training over semantically similar tasks (here `plane` and `car`) leads to less forgetting.
    
    * Setup II

        * Training over a sequence of two classification tasks.

        * Task 1: Four classes where the classes can be grouped into two groups (say `deer`, `dog`, `ship` and `truck`)

        * Task 2: Two related classes, which may be related to group 1 or group 2. For example, the classes could be two animals or two objects.
        
        * After training on the second task, classes (from Task 1), which are in the different group as classes from Task 2, are forgotten less.

    * Conclusion

        * Task representational similarity is a function of both underlying data and optimization procedure.

        * Forgetting is most severe for task representations of intermediate similarity.

        * Representational similarity is necessary but not a sufficient condition for forgetting.

* How does catastrophic forgetting change as the task similarity changes?

    * If the model learns different representations for dissimilar tasks, increasing dissimilarity can help to avoid forgetting.

    * When training the two-task, two-class (per task) CIFAR-10 setup with an "others" class (classes not already used in the setup), the forgetting is reduced.
