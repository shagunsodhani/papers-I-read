---
layout: post
title: Born Again Neural Networks
comments: True
excerpt: The paper explores knowledge distillation (KD) from the perspective of transferring knowledge between 2 networks of identical capacity and reports that these Born Again Networks (BANs) outperform their teachers by significant margins in many cases.
tags: ['2018', 'ICML 2018', 'Knowledge Distillation', 'Knowledge Transfer', AI, ICML, KD]
---

## Introduction

* The paper explores knowledge distillation (KD) from the perspective of transferring knowledge between 2 networks of identical capacity.

* This is in contrast to much of the previous work in KD which has focused on transferring knowledge from a larger network to a smaller network.

* The paper reports that these Born Again Networks (BANs) outperform their teachers by significant margins in many cases.

* [Link to the paper](https://arxiv.org/abs/1805.04770)

## Approach

* The standard KD setting is as follows:
    * Start with an untrained network (or ensemble of networks) and train them for the given task. This network is referred to as the teacher network.
    * Now start with another untrained network (generally of smaller size than the teacher network) and train it using the output of the teacher network. This network is referred to as the student network.

* The paper augments this setting with an extra cross-entropy loss between the output of the teacher and the student networks. The student tried to predict the correct answer while matching the output distribution of the teacher.

* The resulting student network is referred to as BAN - Born Again Network.

* The same approach can be used multiple times (with diminishing returns) where the kth generation student is initialized by knowledge transfer from (k-1)th generation student.

* The output of multiple generation BANs are combined via averaging to produce BANE (Born Again Network Ensemble).

## Dark Knowledge

* [Hinton et al](https://shagunsodhani.in/papers-I-read/Distilling-the-Knowledge-in-a-Neural-Network) suggested that even when the output of the teacher network is incorrect, it contains useful information about the similarity between the output classes. This information is referred to as the "dark knowledge".

* The current paper observed that the gradient of the correct output dimension during distillation and normal supervised training resembles the original gradient up to a  weight factor. This sample specific weight is defined by the value of the teacher's max output.

* This suggests distillation may be performing some kind of importance weighing. To explore this further, the paper considers 2 cases:

    * Confidence Weighted By Teacher Max (CWTM) - where each example in the student's loss function is weighted by the confidence that the teacher has on the prediction for that sample. The student incurs a higher loss if the teacher was more confident about the example.

    * Dark Knowledge with Permuted Predictions (DKPP) - The non-argmax output of teacher's predictive distribution are permuted thus destroying the information about which output classes are related.

* The key effect of these variations is that the covariance between the output classes is lost and classical knowledge distillation would not be sufficient to explain improvements (if any).

## Experiments

### Image Data

* Datasets
    * CIFAR10
    * CIFAR100

* Baselines
    * ResNets
    * DenseNets

* BAN Variants
    * BAN-DenseNet and BAN-ResNet  - Train a sequence of 2 or 3 BANs using DenseNets and ResNets. Different variants constrain BANs to be similar to their teacher or penalize l2-distance between student and teacher activations etc.
    * Two settings with CWTM and DKPP as explained earlier.
    * BAN-Resnet with DenseNet teacher and BAN-DenseNet with ResNet teacher

### Text Data

* Datasets:
    * PTB Dataset

* Baselines
    * CNN-LSTM model

* BAN Variant
    * LSTM

## Results

* BAN student models improved over their teachers in most of the configurations.
* Training BANs across multiple generations leads to saturating improvements.
* The student models exhibit improvements even in the control settings (CWTM and DKPP).
    * One reason could be that the permutation procedure did not remove the higher order moments of output distribution.
    * Improvements in the CWTM model suggests that the pre-trained models can be used to rebalance the training set by giving lesser weight for samples where the teacher's output distribution is more spread.

