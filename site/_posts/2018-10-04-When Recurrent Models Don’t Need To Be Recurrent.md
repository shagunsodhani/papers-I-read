---
layout: post
title: When Recurrent Models Don’t Need To Be Recurrent
comments: True
excerpt: 
tags: ['2018','Natural Language Processing', 'Recurrent Neural Network', AI, NLP, RNN, Theory]
---

## Introduction

* The paper explores "if a well behaved RNN can be replaced by a feed-forward network of comparable size without loss in performance."

* "Well behaved" is defined in terms of control-theoretic notion of stability. This roughly requires that the gradients do not explode over time.

* The paper shows that under the stability assumption, feedforward networks can approximate RNNs for both training and inference. The results are empirically validated as well.

* [Link to the paper](https://arxiv.org/abs/1805.10369)

## Problem Setting

* Consider a general, non linear dynamical system given by a differential state transition map &Phi;<sub>w</sub>. The hidden h<sub>t</sub> = &Phi;<sub>w</sub>(h<sub>t-1</sub>, x<sub>t</sub>).

* Assumptions:
    
    * &Phi; is smooth in w and h.
    * h<sub>0</sub> = 0
    * &Phi;<sub>w</sub>(0, 0) = 0 (can be ensured by translation)

* Stable models are the ones where &Phi; is contractive ie &Phi;<sub>w</sub>(h, x) - &Phi;<sub>w</sub>(h', x) is less than &Lambda; * (h - h')

* For example, in RNN, stability would require that norm(w) is less than (L<sub>p</sub>)<sup>-1</sup> where L<sub>p</sub> is the Lipschitz constant of the point-wise non linearity used.

* The feedforward approximation uses a finite context (of length k) and is a truncated model.

* A non-parametric function f maps the output of the recurrent model to prediction. If f is desired to be a parametric model, its parameters can be pushed to the recurrent model.

## Theoretical Results

* For a &Lambda;-contractive system, it can be proved that for a large k (and additional Lipschitz assumptions) the difference in prediction between the recurrent and truncated mode is negligible.

* If the recurrent model and truncated feed-forward network are initialized at the same point and trained over the same input for N-step, then for an optimal k, the weights of the two models would be very close in the Euclidean space. It can be shown that this small difference does not lead to large gradient differences during subsequent update steps.

* This can be roughly interpreted as - if the gradient descent can train a stable recurrent network, it can also train a feedforward model and vice-versa.

* The stability condition is important as, without that, truncated models would be bad (even for large values of k). Further, it is difficult to show that gradient descent converges to a stationary point.
