---
layout: post
title: Remembering for the Right Reasons - Explanations Reduce Catastrophic Forgetting
comments: True
excerpt: 
tags: ['2020', 'Catastrophic Forgetting', 'Continual Learning', 'Lifelong Learning', 'Replay Buffer', AI, CL, LL]

---

## Introduction

* The paper hypothesizes that catastrophic forgetting can happen if the model can not rely on "reasoning" used for an old datapoint. If that is the case, catastrophic forgetting may be alleviated when the model "remembers" why it made a prediction previously.
* The paper presents a simple instantiation of this hypothesis, in the form of a technique called Remembering for the Right Reasons (RRR).
* The idea is to store model explanations, along with previous examples in the replay buffer. During replay, an additional *explanation loss* is used, along with the regular replay loss.
* [Link to the paper](https://arxiv.org/abs/2010.01528)
* [Link to the code](https://github.com/SaynaEbrahimi/Remembering-for-the-Right-Reasons)

## Setup

* The model is trained over a sequence of data distributions in the class-incremental learning setup. A single-head architecture is used so that the task ID is not required during inference.
* Along with the standard replay buffer ($$M^{rep}$$) for the raw input examples (from different tasks), another replay buffer ($$M^{RRR}$$) is maintained for storing the "explanations" (in the form of saliency maps), corresponding to examples in $$M^{rep}$$.
* RRR is implemented as an L1 loss on the error between the saliency map generated after training on the current task and the saliency map in $$M^{RRR}$$.
* Saliency maps need to be generated while the model is training. This requirement rules out black-box saliency methods, which can be used only after training.
* The gradient-based white-box explainability techniques that are used include:
    * Vanilla backpropagation - Perform a forward pass through the model and take the gradient of the given output class with respect to the input.
    * Backpropagation with SmoothGrad - Saliency maps generated using Vanilla backpropagation can be visually noisy. These maps can be improved by adding pixel-wise Gaussian noise to *n* copies of the image and averaging the resulting gradients. The paper used *n=40*.
    * Gradient-weighted Class Activation Mapping (Grad-CAM) - Uses gradients to determine the importance of feature map activations on a given prediction.
* RRR can be easily used with memory and regularization based approaches.
* The paper combined RRR with the following standard Class Incremental Learning (CIL) models:
    * [iTAML : An incremental task-agnostic meta-learning approach](https://arxiv.org/abs/2003.11652)
    * [End-to-end incremental learning (EEIL)](https://arxiv.org/abs/1807.09536)
    * [Large scale incremental learning (BiC)](https://arxiv.org/abs/1905.13260)
    * [TOpology-Preserving knowledge InCrementer (TOPIC)](https://arxiv.org/abs/2004.10956)
    * [iCaRL: Incremental Classifier and Representation Learning](https://arxiv.org/abs/1611.07725)
    * [Elastic Weight Consolidation](https://arxiv.org/abs/1612.00796)
    * [Learning without forgetting](https://arxiv.org/abs/1606.09282)

## Experiments

### Few-Shiot Class Incremental Learning

* C-way K-shot class incremental learning with C classes and K training samples per class and b base classes to learn as the first task.
* Caltech-UCSD Birds dataset with 100 base classes and remaining 100 classes divided into ten tasks, with three samples per class. The test set is not changed.
* In teems of saliency maps., Grad-CAM is better than Vanilla Backpropagation, which in turn is comparable to SmoothGrad. The same trend is seen in terms of memory overhead, with Grad-CAM having the least memory overhead.
* Adding the RRR loss improves the performance of all the baselines.

### Standard Class Incremental Learning

* CIFAR100 and ImageNet100 with a memory budget of 2000 samples.
* Adding the RRR loss improves all the baselines' performance, and the gains for ImageNet100 are more significant than the gains for CIFAR100.

### How often does the model remember its decision for the right reason?

* The paper uses the Pointing Game (PG) experiment, which uses the ground truth image segmentation to define the true object region.
* If the maximum attention location (in the predicted saliency map) falls inside the objects, it is considered a *hit*, else a *miss*. A *hit* on a previous example is considered a proxy for the model remembering its decision for the right reason.
* The precision and recall are reported for the *hit* metric. Using RRR increases both precision (i.e., less often the model makes the correct decision without looking at the right evidence) and recall (i.e., less frequently does the model makes an incorrect decision, despite looking at the proper evidence).