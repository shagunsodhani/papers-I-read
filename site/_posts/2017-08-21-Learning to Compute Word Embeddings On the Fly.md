---
layout: post
title: Learning to Compute Word Embeddings On the Fly
comments: True
excerpt: The paper proposes to learn a network that can predict the representations of words using auxiliary data (referred to as definitions) such as dictionary definitions, Wikipedia infoboxes, the spelling of the word etc.
tags: ['2017', AI, Embedding, NLP, Out of Vocabulary Words]
---

## Introduction

* Word based language models suffer from the problem of rare or Out of Vocabulary (OOV) words.

* Learning representations for OOV words directly on the end task often results in poor representation.

* The alternative is to replace all the rare words with a single, unique representation (loss of information) or use character level models to obtain word representations (they tend to miss on the semantic relationship).

* The paper proposes to learn a network that can predict the representations of words using auxiliary data (referred to as definitions) such as dictionary definitions, Wikipedia infoboxes, the spelling of the word etc.

* The auxiliary data encoders are trained jointly with the end task to ensure that word representations align with the requirements of the end task.

## Approach

* Given a rare word *w*, let *d(w) = <x<sub>1</sub>, x<sub>2</sub>...>* denote its defination where *x<sub>i</sub>* are words.

* *d(w)* is fed to a *defination reader* network *f* (LSTM) and its last state is used as the *defination embedding e<sub>d</sub>(w)*

* In case *w* has multiple definitions, the embeddings are combined using mean pooling.

* The approach can be extended to in-vocabulary words as well by using the *definition embedding* of such words to update their original embeddings.

## Experiments

* Auxiliary data sources
    * Word definitions from WordNet
    * Spelling of words

* The proposed approach was tested on following tasks:
    
    * Extractive Question Answering over SQuAD
        * Base model from [Xiong et al. 2016](https://arxiv.org/abs/1611.01604)

    * Entailment Prediction over SNLI corpus
        * Base models from [Bowman et al. 2015](https://nlp.stanford.edu/pubs/snli_paper.pdf) and [Chen et al. 2016](https://arxiv.org/abs/1609.06038)

    * One Billion Words Language Modelling

* For all the tasks, models using both spelling and dictionary (SD) outperformed the model using just one.

* While SD does not outperform the Glove model (with full vocabulary), it does bridge the performance gap significantly.

## Future Work

* Multi-token words like "San Francisco" are not accounted for now.

* The model does not handle the rare words which appear in the definition and just replaces them by the <UNK> token. Making the model recursive would be a useful addition.