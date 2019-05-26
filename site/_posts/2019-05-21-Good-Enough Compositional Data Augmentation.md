---
layout: post
title: Good-Enough Compositional Data Augmentation
comments: True
excerpt: 
tags: ['2019', 'Data Augmentation', 'Sequential models', AI, Compositionality, NLP]
---

## Introduction

* The paper introduces a simple data augmentation protocol that provides a good compositional inductive bias for sequential models.

* Synthetic examples are created by taking real sequences and replacing the fragments in sequences which appear in similar environments. This operation is referred to as GECA (Good Enough Compositional Augmentation).

* The underlying idea is that if two fragments of training examples occur in some environment, then any environment where the first fragment appears is also a valid environment for the second fragment.

* [Link to the paper](https://arxiv.org/abs/1904.09545)

## Approach

* Discover substitutable fragments (ie pairs of fragments that co-occur with a common fragment) and use them to generate new sequences by swapping fragments. 

* The current work uses very simple criteria to decide if fragments are substitutable - fragments should occur in at least one lexical environment that is exactly the same. A lexical environment is the k-word window around each span of the fragment.

* Though the idea can be motivated by work in generative syntax and distributional semantics, it would not hold like a physical law when applied to the real data.

* The authors view this tradeoff as a balance between the shortage of training data vs relative frequency of mistake in the proposed data augmentation approach.

## Results

* The approach is evaluated on the SCAN dataset when the model is trained on the short sequence of English commands. Though the dataset augmentation helps the baseline models, it is not surprising given the nature of the SCAN dataset.

* More challenging tasks (for evaluating the proposed approach) are semantic parsing (where the query is represented in the form of &lambda; calculus or SQL and low resource language modeling. While the improvement (in terms of metrics) is sometimes limited, the gains are consistent across different datasets.

* Given that the proposed approach is relatively simple and straightforward, it appears to be quite promising.
