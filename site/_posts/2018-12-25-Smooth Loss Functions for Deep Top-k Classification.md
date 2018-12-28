---
layout: post
title: Smooth Loss Functions for Deep Top-k Classification
comments: True
excerpt: 
tags: ['2018', 'ICLR 2018', 'Loss Function',  AI, ICLR, Loss]
---

## Introduction

* For top-k classification tasks, cross entropy is widely used as the learning objective even though it is the optimal metric only in the limit of infinite data.

* The paper introduces a family of smoothed loss functions that are specially designed for top-k optimization.

* [Paper](https://arxiv.org/abs/1802.07595)

* [Code](https://github.com/oval-group/smooth-topk)

## Idea

* Inspired by the multi-loss SVMs, a surrogate loss (l<sub>k</sub>) is introduced that creates a margin between the ground truth and the kth largest score.

![Equation 1](https://github.com/shagunsodhani/papers-I-read/raw/master/assets/topk/eq1.png)

* Here **s** denotes the output of the classifier model to be learnt, *y* is the ground truth label, *s[p]* denotes the kth largest element of **s** and **s\p** denotes the vector **s** without *p*th element.

* This l<sub>k</sub> loss has two limitations:
    
    * It is continous but not differentiable in *s*.

    * Its weak derivatives have at most 2-nonzero elements.

* The loss can be reformulated by adding and subtracting the k-1 largest scores of **s\y** and *s<sub>y</sub>* and by introducing a temperature parameter &tau;.

![Equation 2](https://github.com/shagunsodhani/papers-I-read/raw/master/assets/topk/eq2.png)

## Properties of L<sub>k&tau;</sub>

* For any &tau; > 0, L<sub>k&tau;</sub> is infinite-differentiable and has non-sparse gradients.

* Under mild conditions, L<sub>k&tau;</sub> apporachs l<sub>k</sub> (in a pointwise sense) as &tau; approaches to 0+<sup>+</sup>.

* It is an upper bound on the actual loss (up to a constant factor).

* It is a generalization of the cross-entropy loss for different values of k, and &tau; and higher margins.


## Computational Challenges

* *nCk* number of terms needs to be evaluated for computing the loss for one sample (n is number of classes).

* Loss L<sub>k&tau;</sub> can be expressed in terms of elementary symmetric polynomials &sigma;<sub>i</sub>(**e**) (sum of all products of i distinct elements of vector e). Thus the challenge is to compute &sigma;<sub>k</sub> efficiently.

### Forward Computation

* Compute &sigma;<sub>k</sub>(**e**) where **e** is a n-dimensional vector and k<< n and e[i]!=0 for all i.

* &sigma;<sub>i</sub>(*e*) can be computed using the coefficients of the polynomial (X+e<sub>1</sub>)(X+e<sub>2</sub>)...(X+e<sub>n</sub>) by divide and conquer approach with polynomial multiplication.

* With some more optimizations (eg log(n) levels of recursion and each level being parallelized on a GPU), the resulting algorithms scale well with n on a GPU.

* Operations are performed in the log-space using the log-sum-exp trick to achieve numerical stability in single floating point precision.

### Backward computation

* The backward pass uses optimizations like computing derivative of &sigma;<sub>j</sub> with respect to e<sub>i</sub> in a recursive manner.

* Appendix of the paper describes these techniques in detail.

## Experiments

* Experiments are performed on CIFAR-100 (with noise) and Imagenet.

* For CIFAR-100 with noise, the labels are randomized with probability p (within the same top-level class).

* The proposed loss function is very robust to both noise and reduction in the amount of training dataset as compared to cross-entropy loss function for both top-k and top-1 performance.