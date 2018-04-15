---
layout: post
title: StarSpace - Embed All The Things!
comments: True
excerpt: The paper describes a general purpose neural embedding model where different type of entities (described in terms of discrete features) are embedded in a common vector space.
tags: ['2017', 'Graph Representation', 'Multi Task', 'Word Vectors', 'Representation Learning', Embedding, Graph, NLP]
---

## Introduction

* The paper describes a general purpose neural embedding model where different type of entities (described in terms of discrete features) are embedded in a common vector space.

* A similarity function is learnt to compare these entities in a meaningful way and score their similarity. The definition of the similarity function could depend on the downstream task where the embeddings are used.

* [Link to the paper](https://arxiv.org/abs/1709.03856)

* [Link to the implementation](https://github.com/facebookresearch/StarSpace)

## Approach

* Each entity is described as a set of discrete features. For example, for the recommendation use case, the users may be described as a bag-of-words of movies they have liked. For the search use case, the document may be described as a bag-of-words of words they are made up of.

* Given a dataset and a task at hand, generate a set of positive samples *E = (a, b)* such that *a* is the input to the task (from the dataset) and *b* is the expected label(answer/entity) for the given task.

* Similarly, generate another set of negative samples *E <sup>-</sup> = (a, b<sub>i</sub><sup>-</sup>)* such that *b<sub>i</sub><sup>-</sup>* is one of the incorrect label(answer/entity) for the given task. The incorrect entity can be sampled randomly from the set of candidate entities. Multiple incorrect samples could be generated for each positive example. These incorrect samples are indexed using *i*.

* For example, in case of supervised learning problem like document classification, *a* would be one of the documents (probably described in terms of words), *b* is the correct label and *b<sub>i</sub><sup>-</sup>)* is one of the randomly sampled label from set of all the labels (excluding the correct label).

* In case of collaborative filtering, *a* would be the user (either described as a discrete entity like a userid or in terms of items purchased so far), *b* is the next item the user purchases and *b<sub>i</sub><sup>-</sup>)* is one of the randomly sampled item from the set of all the items.

* A similarity function is chosen to compare the representation of entities of type *a* and *b*. The paper considered cosine similarity and inner product and observed that cosine similarity works better for the case with a large number of entities.

* A loss function compares the similarity between positive pairs *(a, b)* and *(a, b<sub>i</sub><sup>-</sup>)*. The paper considered margin ranking loss and negative log loss of softmax and reported that margin ranking loss works better.

* The norm of embeddings is capped at 1.

## Observations

* The same model architecture is applied to a variety of tasks including multi-class classification, multi-label classification, collaborative filtering, content-based recommendation, link prediction, information retrieval, word embeddings and sentence embeddings.

* The model provides a strong baseline on all the tasks and performs at par with much more complicated and task-specific networks.

