---
layout: post
title: Large Memory Layers with Product Keys
comments: True
excerpt: 
tags: ['2019', 'Key Value', 'Natural Language Processing', AI, Attention, Memory, NLP]

---

## Introduction

* The paper proposes a structured key-value memory layer that:
    * Can scale to a very large size (and capacity).
    * Has very low computational overhead.
    * Supports exact search in the keyspace.
    * Can be easily integrated with neural networks.

* [Link to the paper](https://arxiv.org/abs/1907.05242)

## Architecture

* The memory layer is composed of 3 components:
    
    * **Query Network**

        * Maps input to a latent space.
        * Can be implemented as a feed-forward network.
        * Adding batch-norm on top of the query network helps to spread out keys.

    * **Key selection module**

        * Lets say there are a total of *K* keys of dimensionality *d<sub>q</sub>* of which we want to select top *k* keys.
        * Partition the set of keys into two sets of *subkeys* (say *Q<sub>1</sub>* and *Q<sub>2</sub>*) where each subset has *K* keys of dimensionality *d_q/2*.
        * The query is split into two subqueries (say *q<sub>1</sub>* and *q<sub>2</sub>*). 
        * Each of these two queries are compared with every query in their corresponding set of *subkeys*.
        * For example, *q<sub>1</sub>* is compared with every query is *Q<sub>1</sub>*.
        * Top *k* ranked keys are selected from each set to create two new sets *C<sub>1</sub>* and *C2<sub>2</sub>*.
        * The keys from these two sets are combined uder the concatenation operator to obtain *k<sub>2</sub>* vectors.
        * the final top *k* (concatenated) keys are searched from these *k<sup>2* keys.
        * The overall complexity is $O((\sqrt K + k^2) \times d_q)$ where *K* is the total number of keys (whiuc)

    * **Value lookup table**

        * The values (corresponding to selected subkeys) are aggregated (using weighted sum operation) to obtain the output.

* All the parameters are trainable, though, in practice, only the selected *k* memory slots are updated.

* Using Multihead attention mechanism helps to improve the performance further.

## Experiments

* 1 or more feedforward layers in transformers are placed by the memory layers.

* The model is evaluated on large scale language modeling tasks with 140 Gb of data from common crawl corpora (28n billion words).

* Evaluation metrics

    * Perplexity on the test set.

    * Fraction of accessed values.

    * KL divergence between the (normalized) weights of key access and uniform distribution.

    * The last two metrics are used together to determine how well the keys are utilized.

## Results

* Given the large size of the training dataset, adding more layers to the transformer model helps.

* Effect of using memory layer is more powerful than the effect of adding new layers to the transformer. For example, a 12 layer transformer + memory layer outperforms a 24 layer transformer while being almost twice as fast.

* The best position to place the memory is at an intermediate layer and placing the memory layer right after the input or just before the softmax layer does not work well in practice.

