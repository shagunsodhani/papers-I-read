---
layout: post
title: Your Classifier is Secretly an Energy Based Model and You Should Treat it Like One
comments: True
excerpt: 
tags: ['2019', 'ICLR 2020', 'Adversarial Robustness', 'Energy-Based Models',  'Generative Models', 'Hybrid Models', 'Out of Distribution', 'Outlier Detection', 'Out of Distribution Detection',  AI, Adversarial, Calibration, EBM, ICLR, Robustness]

---

## Introduction

* The paper proposed a framework for joint modeling of labels and data by interpreting a discriminative classifier *p(y\|x)* as an energy-based model *p(x, y)*.

* Joint modeling provides benefits like improved calibration (i.e., the predictive confidence should align with the miss classification rate), robustness, and out of order distribution.

* [Link to the paper](https://arxiv.org/abs/1912.03263)

## Motivation

* Consider a standard classifier $f_{\theta}(x)$ which produces a k-dimensional vector of logits.

* $p_{\theta}(y \| x) = softmax(f_{\theta}(x)[y])$

* Uisng concepts from energy based models, we write $p_{\theta}(x, y) = \frac{exp(-E_{\theta}(x, y))}{Z_{\theta}}$ where $E_{\theta}(x, y) = -f_{\theta}(x)[y]$

* $p_{\theta}(x) = \sum_{y}{ \frac{exp(-E_{\theta}(x, y))}{Z_{\theta}}}$

* $E_{\theta}(x) = -LogSumExp_y(f_{\theta}(x)[y])$

* Note that in the standard discriminative setup, shiting the logits $f_{\theta}(x)$ does not affect the model but it affects $p_{\theta}(x)$.

* Computing $p_{\theta}(y \| x)$ using $p_{\theta}(x, y)$ and $p_{\theta}(x)$ gives back the same softmax parameterization as before.

* This reinterpreted classifier is referred to as a Joint Energy-based Model (JEM).

## Optimization

* The log-liklihood of the data can be factoized as $log p_{\theta}(x, y) = log p_{\theta}(x) + log p_{\theta}(y \| x)$.

* The second factor can be trained using the standard CE loss. In contrast, the first factor can be trained using a sampler based on Stochastic Gradient Langevin Dynamics.

## Results

### Hybrid Modelling

* Datasets: CIFAR10, CIFAR100, SVHN.

* Metrics: Inception Score, Frechet Inception Distance

* JEM outperforms generative, discriminative, and hybrid models on both generative and discriminative tasks.

### Calibration

* A calibrated classifier is the one where the predictive confidence aligns with the misclassification rate.

* Dataset: CIFAR100

* JEM improves calibration while retaining high accuracy.

### Out of Distribution (OOD) Detection

* One way to detect OOD samples is to learn a density model that assigns a higher likelihood to in-distribution examples and lower likelihood to out of distribution examples.

* JEM consistently assigns a higher likelihood to in-distribution examples.

* The paper also proposes an alternate metric called *approximate mass* to detect OOD examples.

* The intuition is that a point could have likelihood but be impossible to sample because its surroundings have a very low density.

* On the other hand, the in-distribution data points would lie in a region of high probability mass.

* Hence the norm of the gradient of log density could provide a useful signal to detect OOD examples.

### Robustness

* JEM is more robust to adversarial attacks as compared to discriminative classifiers.