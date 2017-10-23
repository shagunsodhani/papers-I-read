---
layout: post
title: Swish - a Self-Gated Activation Function
comments: True
excerpt: The paper presents a new, smooth, non-monotonic activation function called Swish.
tags: ['2017', 'Activation Function', 'Self Gated', AI, SOTA]
---

## Introduction

* The paper presents a new activation function called Swish with formulation *f(x) = x.sigmod(x)* and its parameterised version called Swish-&beta; where *f(x, &beta;) = 2x.sigmoid(&beta;.x)* and &beta; is a training parameter.

* The paper shows that Swish is consistently able to outperform RELU and other activations functions over a variety of datasets (CIFAR, ImageNet, WMT2014) though by small margins only in some cases.

* [Link to the paper](https://arxiv.org/abs/1710.05941)

## Properties of Swish

* ![Plot Of Swish](https://raw.githubusercontent.com/shagunsodhani/papers-I-read/master/assets/Swish/plot.png)

* Smooth, non-monotonic function.

* Swish-&beta; can be thought of as a smooth function that interpolates between a linear function and RELU.

* Uses self-gating mechanism (that is, it uses its own value to gate itself). Gating generally uses multiple scalar inputs but since self-gating uses a single scalar input, it can be used to replace activation functions which are generally pointwise.

* Being unbounded on the x>0 side, it avoids saturation when training is slow due to near 0 gradients.

* Being bounded below induces a kind of regularization effect as large, negative inputs are forgotten.

* Since the Swish function is smooth, the output landscape and the loss landscape are also smooth. A smooth landscape should be more traversable and less sensitive to initialization and learning rates.

## Criticism

* Swish is much more complicated than ReLU (when weighted against the small improvements that are provided) so it might not end up with as strong an adoption as ReLU.