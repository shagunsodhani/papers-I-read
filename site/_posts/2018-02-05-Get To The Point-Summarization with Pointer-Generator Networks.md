---
layout: post
title: Get To The Point - Summarization with Pointer-Generator Networks
comments: True
excerpt: The paper proposes a hybrid Pointer-Generator network along with the use of a coverage vector for the task of article summarization.
tags: ['2017', 'ACL 2017', 'Abstract Summarization', 'Pointer Network', ACL, AI, NLP, SOTA, Summarization]
---

## Introduction

* [Sequence-to-Sequence models](https://gist.github.com/shagunsodhani/a2915921d7d0ac5cfd0e379025acfb9f) have made abstract summarization viable but they still suffer from issues like *out of vocabulary* words and repetitive sentences.

* The paper proposes to overcome these limitations by using a hybrid Pointer-Generator network (to copy words from the source text) and a *coverage* vector that keeps track of content that has already been summarized so as to discourage repetition.

* [Link to the paper](https://arxiv.org/abs/1704.04368)

* [Code](https://github.com/abisee/pointer-generator)

## Model

### Pointer Generator Network

* It is a hybrid model between the Sequence-to-Sequence network and [Pointer Network](https://shagunsodhani.in/papers-I-read/Pointer-Networks) such that when generating a word, the model decides whether the word would be generated using the softmax vocabulary (Sequence-to-Sequence) or using the source vocabulary (Pointer Network).

* Since the model can choose a word from the source vocabulary, the issue of *out of vocabulary* words is handled.

### Coverage Mechanism

* The model maintains a *coverage* vector which is the sum of attention distributions over all previous decoder timesteps.

* This *coverage* vector is fed as an input to the attention mechanism.

* A *coverage loss* is added to prevent the model from repeatedly attending to the same word.

* The idea is to capture how much coverage different words have already received from the attention mechanism.

## Observation

* Model when evaluated on CNN/Daily Mail summarization task, outperforms the state-of-the-art by at least 2 ROUGE points though it still does not outperform the lead-3 baseline.

* Lead-3 baseline uses first 3 sentences as the summary of the article which should be a strong baseline given that the dataset is actually about news articles.

* The model is initially trained without coverage and then finetuned with the coverage loss.

* During training, the model first learns how to copy words and then how to generate words (p<sup>gen</sup> starts from 0.3 and converges to 0.53).

* During testing, the model strongly prefers copying over generating (p<sup>gen</sup> = 0.17).

* Further, whenever the model is at beginning of sentences or at the join between switched-together fragments, it prefers to generate a word instead of copying one from the source language.

* The overall model is very simple, neat and interpretable and also performs well in practice.