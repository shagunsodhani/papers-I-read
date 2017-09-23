---
layout: post
title: A Decomposable Attention Model for Natural Language Inference
comments: True
excerpt: The paper proposes an attention based mechanism to decompose the problem of Natural Language Inference (NLI) into parallelizable subproblems
tags: ['2016', 'EMNLP 2016', AI, Attention, EMNLP, Natural Language Inference, NLP, SOTA]
---

### Introduction

* The paper proposes an attention based mechanism to decompose the problem of Natural Language Inference (NLI) into parallelizable subproblems.
* Further, it uses much fewer parameters as compared to any other model while obtaining state of the art results.
* [Link to the paper](https://arxiv.org/abs/1606.01933)
* The motivation behind the paper is that the tasks like NLI do not require deep modelling of the sentence structure and comparison of local text substructures followed by aggregation can also work very well

### Approach

* Given two sentences **a** and **b**, the model has to predict whether they have an "entailment" relationship, "neutral" relationship or "contradiction" relationship.

* **Embed**
    * All the words are mapped to their corresponding word vector representation. In subsequent steps, "word" refers to the word vector representation of the actual word.

* **Attend**
    * For each word *i* in **a** and *j* in **b**, obtain unnormalized attention weights *e(i, j)=F(i)<sup>T</sup>F(j) where F is a feed-forward neural network.
    * For *i*, compute a &beta;<sub>i</sub> by performing softmax-like normalization of *j* using *e(i, j)* as the weight and normalizing for all words *j* in **b**.
    * &beta;<sub>i</sub> captures the subphrase in **b** that is softly aligned to *a*.
    * Similarly compute &alpha;<sub>j</sub> for *j*.

* **Compare**
    * Create two set of comparison vectors, one for **a** and another for **b**
    * For **a**, **v<sub>1, i</sub>** = G(concatenate(i, &beta;<sub>i</sub>)).
    * Similarly for **b**, **v<sub>2, j</sub>** = G(concatenate(j, &alpha;<sub>j</sub>))
    * G is another feed-forward neural network.

* **Aggregate**
    * Aggregate over the two set of comparison vectors to obtain **v<sub>1</sub>** and **v<sub>2</sub>**.
    * Feed the aggregated results through the final classifier layer.
    * Multi-class cross-entropy loss function.

* The paper also explains how this representation can be augmented using intra-sentence attention to the model compositional relationship between words.

### Computational Complexity

* Computationally, the proposed model is asymptotically as good as LSTM with attention. 
* Assuming that dimensionality of word vectors > length of the sentence (reasonable for the given SNLI dataset), the model is asymptotically as good as regular LSTM.
* Further, the model has the advantage of being parallelizable.

### Experiment

* On Stanford Natural Language Inference (SNLI) dataset, the proposed model achieves the state of the art results even when it uses an order of magnitude lesser parameters than the next best model.
* Adding intra-sentence attention further improve the test accuracy by 0.5 percent.

### Notes

* A similar approach could be tried on paraphrase detection problem as even that problem should not require very deep sentence representation. [Quora Duplicate Question Detection Challenege](https://data.quora.com/First-Quora-Dataset-Release-Question-Pairs)  would have been an ideal dataset but it has a lot of out-of-vocabulary information related to named entities which need to be accounted for.