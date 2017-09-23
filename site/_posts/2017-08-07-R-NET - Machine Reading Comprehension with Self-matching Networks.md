---
layout: post
title: R-NET - Machine Reading Comprehension with Self-matching Networks
comments: True
excerpt: Reasoning Network (ReasoNet) uses reinforcement learning (RL) to decide how many times a document should be read.
tags: ['2017', AI, Machine Comprehension, NLP, QA, RL, SOTA]
---

## Introduction

* R-NET is an end-to-end trained neural network model for machine comprehension.

* It starts by matching the question and the given passage (using gated attention based RNN) to obtain question-aware passage representation.

* Next, it uses a self-matching attention mechanism to refine the passage representation by matching the passage against itself.

* Lastly, it uses pointer networks to determine the position of the answer in the passage.

* [Link to the paper](https://www.microsoft.com/en-us/research/publication/mrc/)

## Datasets

* SQuAD

* MS-MARCO

## Architecture

* Question / Passage Encoder
    
    * Concatenate the word level and character level embeddings for each word and feed into a bidirectional GRU to obtain question and passage representation.

* Gated Attention based RNN

    * Given question and passage representation, sentence pair representation is generated via soft-alignment of the words in the question and in the passage.

    * The newly added gate captures the relation between the question and the current passage word as only some parts of the passage are relevant for answering the given question.

* Self Matching Attention

    * The passage representation obtained so far would not capture most of the context.

    * So the current representation is matched against itself so as to collect evidence from the entire passage and encode the evidence relevant to the current passage word and question.

* Output Layer

    * Use pointer network (initialized using attention pooling over answer representation) to predict the position of the answer.

    * Loss function is the sum of negative log probabilities of start and end positions.

* Results
    
    * R-NET is ranked second on [SQuAD Leaderboard](https://rajpurkar.github.io/SQuAD-explorer/) as of 7th August, 2017 and achieves best-published results on MS-MARCO dataset.

    * Using ideas like sentence ranking, using syntax information performing multihop inference and augmenting question dataset (using seqToseq network) do not help in improving the performance.