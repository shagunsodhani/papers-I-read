---
layout: post
title: Principled Detection of Out-of-Distribution Examples in Neural Networks
comments: True
excerpt: The paper proposes a system which can detect out-of-distribution examples without changing the pre-trained model itself.
tags: ['2017', AI, CV, Out of Distribution, Softmax]
---

## Problem Statement

* Given a pre-trained neural network, which is trained using data from some distribution P (referred to as in-distribution data), the task is to detect the examples coming from a distribution Q which is different from P (referred to as out-of-distribution data).

* For example, if a digit recognizer neural network is trained using MNIST images, an out-of-distribution example would be images of animals.

* Neural Networks can make high confidence predictions even in such cases where the input is unrecognisable or irrelevant.

* The paper proposes *ODIN* which can detect such out-of-distribution examples without changing the pre-trained model itself.

* [Link to the paper](https://arxiv.org/abs/1706.02690)

## ODIN

* Uses 2 major techniques

    * **Temperature Scaling**
        * Softmax classifier for the classification network can be written as:

            *p<sub>i</sub>(x, T) = exp(f<sub>i</sub>(x)/T) / sum(exp(f<sub>j</sub>(x)/T))*

        where *x* is the input, *p* is the softmax probability and *T* is the temperature scaling parameter.

        * Increasing *T* (up to some extent) boosts the performance in distinguishing in-distribution and out-of-distribution examples.

    * **Input Preprocessing**
        * Add small perturbations to the input (image) before feeding it into the network.

        * *x_perturbed = x - &epsilon; * sign(-&delta;<sub>x</sub>log(p<sub>y</sub>(x, T)))*

        where &epsilon; is the perturbation magnitude    

        * The perturbations are such that softmax scores between in-distribution and out-of-distribution samples become separable.

* Given an input (image), first perturb the input.
* Feed the perturbed input to the network to get its softmax score.
* If the softmax score is greater than some threshold, mark the input as in-distribution and feed in the unperturbed version of the input to the network for classification.
* Otherwise, mark the input as out-of-distribution.
* For detailed mathematical treatment, refer section 6 and appendix in the [paper](https://arxiv.org/abs/1706.02690)

## Experiments

* Code available on [github](https://github.com/ShiyuLiang/odin-pytorch)

* Models

    * DenseNet with depth L = 100 and growth rate k = 12
    * Wide ResNet with depth = 28 and widen factor = 10

* In-Distribution Datasets
    
    * CIFAR-10
    * CIFAR-100

* Out-of-Distribution Datasets
    
    * TinyImageNet
    * LSUN
    * iSUN
    * Gaussian Noise

* Metrics

    * False Positive Rate at 95% True Positive Rate
    * Detection Error - minimum misclassification probability over all thresholds
    * Area Under the Receiver Operating Characteristic Curve
    * Area Under the Precision-Recall Curve

* ODIN outperforms the baseline across all datasets and all models by a good margin.

## Notes

* Very simple and straightforward approach with theoretical justification under some conditions.
* Limited to examples from Vision so can not judge its applicability for NLP tasks.