---
layout: post
title: Pointer Networks
comments: True
excerpt: The paper introduces a novel architecture that generates an output sequence such that the elements of the output sequence are discrete tokens corresponding to positions in the input sequence.
tags: ['2015', 'NIPS 2015', 'Seq2Seq', AI, NIPS, NLP, Softmax]
---

## Introduction

* The paper introduces a novel architecture that generates an output sequence such that the elements of the output sequence are discrete tokens corresponding to positions in the input sequence.

* Such a problem can not be solved using [Seq2Seq](https://gist.github.com/shagunsodhani/a2915921d7d0ac5cfd0e379025acfb9f) or Neural Turing Machines as the size of the output softmax is variable (as it depends on the size of the input sequence).

* [Link to the paper](https://arxiv.org/abs/1506.03134)

## Architecture

* Traditional attention-base sequence-to-sequence models compute an attention vector for each step of the output decoder and use that to blend the individual context vectors of the input into a single, consolidated attention vector. This attention vector is used to compute a fixed size softmax.

* In Pointer Nets, the normalized attention vector (over all the tokens in the input sequence) is normalized and treated as the softmax output over the input tokens.

* So Pointer Net is a very simple modification of the attention model. 

## Application

* Any problem where the size of the output depends on the size of the input because of which fixed length softmax is ruled out.

* eg combinatorial problems such as planar convex hull where the size of the output would depend on the size of the input.

## Evaluation

* The paper considers the following 3 problems:
    
    * Convex Hull
    * Delaunay triangulations
    * Travelling Salesman Problem (TSP)

* Since some of the problems are NP hard, the paper considers approximate solutions whereever the exact solutions are not feasible to compute.

* The authors used the exact same architecture and model parameters of all the instances of the 3 problems to show the generality of the model.

* The proosed Pointer Nets outperforms LSTMs and LSTMs with attention and can generalise quite well for much larger sequences. 

* Interestingly, the order in which the inputs are fed to the system affects its performance. The authors discussed this apsect in their subsequent paper titled [Order Matters: Sequence To Sequence for Sets](https://arxiv.org/pdf/1511.06391v4.pdf)
