---
layout: post
title: A Semantic Loss Function for Deep Learning with Symbolic Knowledge
comments: True
excerpt: 
tags: ['2018', 'ICML 2018', 'Loss Function', 'Semantic Loss', 'Symbolic Knowledge', AI, ICML, Loss]
---

## Introduction

* The paper proposes an approach for using symbolic knowledge in deep learning systems. These constraints are often expressed as boolean constraints on the output of the deep learning system and directly incorporating these constraints break the differentiability of the system.

* [Link to the paper](https://arxiv.org/abs/1711.11157)

## Problem Setting

* The model is given some input data to perform predictions and symbolic knowledge is provided in form of boolean constraints like exactly-one constraint for one-hot output encoding.

* Most approaches tend to encode the symbolic knowledge in the vector space embedding to keep the model pipeline differentiable. In this process, the precise meaning of symbolic knowledge is often lost.

* A differentiable "semantic loss" is derived which captures the meaning of the constraint while being independent of its syntax.

## Terminology

* A state **x** (state refers to the instantiation of boolean variables) satisfies a sentence *a* if *a* evaluates to true when using the variables as specified by **x**.

* A sentence *a* entails another sentence *b* if all states that satisfy *a* also satisfy *b*.

* The row output vector of the neural network is denoted as *p* where each value in *p* denotes the probability of an output.

* Three different output constraints are studied:

    * *Exactly-one constraint* 

        * Exactly one value in *p* should be true.
        * Can be expressed in boolean logic as follows: Let (x1, x2, ..., xn) be variables in *p*. Then (not xi or not xj) for all pair of variables and (x1 or x2 or ... xn).

    * *Valid Simple Path Constraint*
        * Set of edges must form a valid path.

    * *Ordering Constraint*
        * Defining an ordering over the variables.

## Semantic Loss

* The semantic loss *L<sup>s</sup>(a, p)* is a function of a propositional logic sentence *a* (the symbolic knowldge constraint) and *p* (output of the neural network).

* *a* is defined over variables (x1, ..., xn) and *p* is interpreted as a vector of probabilities corresponding to these variables *xi's*.

* The semantic loss is directly proportional to the negative log likelihood of generating a state that satisfies the constraints when sampling values according to the distribution *p*.

## Main Axioms and Insights

* **Monotonicity**
    * If a sentence *a* entails another sentence *b* then for any given *p*, *L<sup>s</sup>(a, p) > L<sup>s</sup>(b, p)* ie adding more constraints cannot decrease the semantic loss.

* **Semantic Equivalence**
    * If two sentences are logically equivalent, their semantic loss is the same.

* **Identity**
    * For any given sentence *a*, its representation as a sentence is equivalent to its representation as a deterministic vector ie writing the "one-hot" constraint as a boolean expression is equivalent to a one-hot vector.

* **Satisfaction**
    * If *p* entails the sentence *a* then *L<sup>s</sup>(a, p) = 0*.

* **Label-literal correspondence**
    * When the constraint is defined in terms of a single variable, it can be interpreted as the supervised label.
    * Hence the semantic loss in case of a single variable should be equivalent to the cross-entropy loss.

* **Truth**
    * The semantic loss of a true sentence is 0

* **Non-negativity**
    * Semantic loss should always be non-negative.

* Probabilities of variables that are not part of the constraint, do not affect the semantic loss.

* It can be shown that the semantic loss function satisfies all these axioms (and the other axioms specified in the paper) and is the only function to do so, up to a multiplicative constant.

## Experimental Evaluation

* Semantic Loss is used in the semi-supervised setting for Permuted MNIST, Fashion MNIST and CIFAR-10.

* The key takeaway is that using semantic loss improves the performance of the state-of-the-art models for Fashion MNIST and CIFAR-10.

* One downside is that the effectiveness of the semantic loss in this type of constraint strongly depends on the performance of the underlying model. Further, the semantic loss does not improve the performance in case of fully supervised scenario.

* Further experiments are performed to evaluate the performance of the semantic loss on complex constraints. Since these tasks aim to highlight the effect of using semantic loss, only simple models (MLPs) are evaluated.

## Tractability of Semantic Loss

* The semantic loss is similar to the automated reasoning task called as weight model counting (wmc).

* Circuit compiler techniques can be used to compute wmc while allowing backpropagation.

## Notes

* The proposed idea is simple and intuitive and the results on semi-supervised classification task are quite good. It would be interesting to extend and scale this method for more complex constraints. 
