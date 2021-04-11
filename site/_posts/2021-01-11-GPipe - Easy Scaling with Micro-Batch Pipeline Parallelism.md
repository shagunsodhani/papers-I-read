---
layout: post
title: GPipe - Easy Scaling with Micro-Batch Pipeline Parallelism
comments: True
excerpt: 
tags: ['2018', 'Distributed Computing', 'Model Parallelism', 'NeurIPS 2019', AI, Engineering, NeurIPS, Scale, Systems]

---

## Introduction

* The paper introduces GPipe, a pipeline parallelism library for scaling networks that can be expressed as a sequence of layers.

* [Link to the paper](https://arxiv.org/abs/1811.06965)

## Design

* Consider training a deep neural network with *L* layers using *K* accelerators (say GPUs).

* Each of the *i<sup>th</sup>* layer has its *forward* function *f<sub>i</sub>*, *backward* function *b<sub>i</sub>*, weights *w<sub>i</sub>* and a cost *c<sub>i</sub>* (say the memory footprint or computational time).

* GPipe partitions this network into *K* cells and places the *i<sup>th</sup>* cell on the *i<sup>th</sup>* accelerator. Output from the *i<sup>th</sup>* accelerator is passed to the *i+1<sup>th</sup>* accelerator as input.

* During the forward pass, the input batch (of size *N*) is divided into *M* equal micro-batches. These micro-batches are pipelined through the *K* accelerators one after another. 

* During the backward pass, gradients are computed for each micro-batch. The gradients are accumulated and applied at the end of each minibatch.

* In batch normalization, the statistics are computed over each micro-batch (used during training) and mini-batch (used during evaluation).

* Micro-batching improves over the naive mode parallelism approach by reducing the underutilization of resources (due to the network's sequential dependencies).

## Performance Optimization

* GPipe supports re-materialization (or checkpointing), i.e., during the forward pass, only the output activations (at partition boundaries) are stored.

* During backward pass, the forward function is recomputed at each accelerator. This trades off the memory requirement with increased time.

* One potential downside is that partitioning can introduce some idle time per accelerator (referred to as the bubble overhead). However, with a sufficiently large number of micro-batches ( more than 4 times the number of partitions), the bubble overhead is negligible.

## Performance Analysis

* Two different types of model architectures are compared: AmoebaNet convolutional model and Transformer sequence-to-sequence model.

* For AmoebaNet, the size of the largest trainable model (on a single 8GB Cloud TPU v2) increases from 82M to 318M. Further, a 1.8 billion parameter model can be trained on 8 accelerators (25x improvement in size using GPipe).

* For transformers, GPipe scales the model size to 83.9 B parameters with 128 partitions (298x improvement in size compared to a single accelerator).

* Since the computation is evenly distributed across transformer layers, the training throughput scales almost linearly with the number of devices. 

* Quantitative experiments on ImageNet and multilingual machine translation show that models can be effectively trained using GPipe.
