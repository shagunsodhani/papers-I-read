---
layout: post
title: Ask Me Anything -  Dynamic Memory Networks for Natural Language Processing
comments: True
excerpt: Dynamic Memory Networks (DMN) is a neural network based general framework that can be used for tasks like sequence tagging, classification, sequence to sequence and question answering requiring transitive reasoning.
tags: ['2016', AI, Attention, NLP, POS, QA, Sentiment Analysis, SOTA]
---

## Introduction

* Dynamic Memory Networks (DMN) is a neural network based general framework that can be used for tasks like sequence tagging, classification, sequence to sequence and question answering requiring transitive reasoning.

* The basic idea is that all these tasks can be modelled as question answering task in general and a common architecture could be used for solving them.

* [Link to the paper](https://arxiv.org/abs/1506.07285)

## Architecture

* DMN takes as input a document(sentence, story, article etc) and a question which is to be answered given the document.

### Input Module

* Concatenate all the sentences (or facts) in the document and encode them by feeding the word embeddings of the text to a GRU.

* Each time a sentence ends, extract the hidden representation of the GRU till that point and use as the encoded representation of the sentence.

### Question Module

* Similarly, feed the question to a GRU to obtain its representation.

### Episodic Memory Module

* Episodic memory consists of an attention mechanism and a recurrent network with which it updates its memory.

* During each iteration, the network generates an episode *e* by attending over the representation of the sentences, question and the previous memory.

* The episodic memory is updated using the current episode and the previous memory.

* Depending on the amount of supervision available, the network may perform multiple passes. eg, in the bAbI dataset, some tasks specify how many passes would be needed and which sentence should be attended to in each pass. For others, a fixed number of passes are made.

* Multiple passes allow the network to perform transitive inference.

### Attention Mechanism

* Given the input representation *c*, memory *m* and question *q*, produce a scalar score using a 2-layer feedforward network, to use as attention mechanism.

* A separate GRU encodes the input representation and weights it by the attention.

* Final state of the GRU is fed to the answer module.

### Answer Module

* Use a GRU (initialized with the final state of the episodic module) and at each timestep, feed it the question vector, last hidden state of the same GRU and the previously predicted output.

### Training

* There are two possible losses:
    * Cross-entropy loss of the predicted answer (all datasets)
    * Cross-entropy loss of the attention supervision (for datasets like bAbI)

## Experiments

### Question Answering

* bAbI Dataset

* For most tasks, DMN either outperforms or performs as good as Memory Networks.

* For tasks like answering with 2 or 3 supporting facts, DMN lags because of limitation of RNN in modelling long sentences.

### Text Classification

* Stanford Sentiment Treebank Dataset

* DMN outperforms all the baselines for both binary and fine-grained sentiment analysis.

### Sequence Tagging

* Wall Street Journal Dataset

* DMN archives state of the art accuracy of 97.56%

## Observations

* Multiple passes help in reasoning tasks but not so much for sentiment/POS tags.

* Attention in the case of 2-iteration DMN is more focused than attention in 1-iteration DMN.

* For 2-iteration DMN, attention in the second iteration focuses only on relevant words and less attention is paid to words that lose their relevance in the context of the entire document.

## Notes

* It would be interesting to put some mechanism in place to determine the number of episodes that should be generated before an answer is predicted. A naive way would be to predict the answer after each episode and check if the softmax score of the predicted answer is more than a threshold. 

* Alternatively, the softmax score and other information could be fed to a Reinforcement Learning (RL) agent which decided if the document should be read again. So every time an episode is generated, the state is passed to the RL agent which decides if another iteration should be performed. If it decides to predict the answer and correct answer is generated, the agent gets a large +ve reward else a large -ve reward.

* To discourage unnecessary iterations, a small -ve reward could be given everytime the agent decides to perform another iteration.