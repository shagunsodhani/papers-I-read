---
layout: post
title: Stylistic Transfer in Natural Language Generation Systems Using Recurrent Neural Networks
comments: True
excerpt: The paper explores the problem of style transfer in natural language generation.
tags: ['2016', 'ACL 2016', ACL, AI, NLG, NLP, Workshop]
---

## Introduction

* [This workshop paper](https://aclweb.org/anthology/W/W16/W16-6010.pdf) explores the problem of style transfer in natural language generation (NLG).
* One possible manifestation would be rewriting technical articles in an easy-to-understate manner.

## Challenges

* Identifying relevant stylistic cues and using them to control text generation in NLG systems.
* Absence of a large amount of training data.

## Pitch

* Using Recurrent Neural Networks (RNNs) to disentangle the style from semantic content.
* Autoencoder model with two components - one for learning style and another for learning content.
* This allows for "style" component to be replaced while keeping the "content" component same, resulting in a style transfer.
* One way to think about this is - the encoder generates a 100-dimensional vector. In this, the first 50 entries, correspond to the "style" component and remaining to the "content" component.
* The proposal is that the loss function should be modified to include a cross-covariance term for ensuring disentanglement.
* I think one way of doing this is to have two loss functions:
    * The **first loss** function ensures that the input sentence is decoded properly into the target sentence. This loss is computed for each sentence.
    * The **second loss** ensures that the first 50 entries across all the encoded represenations are are correlated. This loss operates at the batch level.
    * The **total loss** is the weighted sum of these 2 losses.

## Possible Datasets

* [Complete works of Shakespeare](http://norvig.com/ngrams/shakespeare.txt)
* [Wikpedia Kaggle dataset](https://www.kaggle.com/c/wikichallenge/data)
* [Oxford Text Archive](https://ota.ox.ac.uk/)
* Twitter data

## Possible Metrics

* Soundness - is the generated text entailed with the input sentence.
* Coherence - free of grammatical errors, proper word usage etc.
* Effectiveness - how effective was the style transfer
* Since some of the metrics are subjective, human evaluators also need to be employed.