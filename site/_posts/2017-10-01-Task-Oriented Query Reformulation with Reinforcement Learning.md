---
layout: post
title: Task-Oriented Query Reformulation with Reinforcement Learning
comments: True
excerpt: The paper introduces a query reformulation system that rewrites a query to maximise the number of "relevant" documents that are extracted from a given black box search engine.
tags: ['2017', 'EMNLP 2017', 'Information Retrieval', AI, EMNLP, NLP, RL]
---

## Introduction

* The paper introduces a query reformulation system that rewrites a query to maximise the number of "relevant" documents that are extracted from a given black box search engine.
* A Reinforcement Learning (RL) agent selects the terms that are to be added to the reformulated query and the rewards are decided on the basis of document recall. 
* [Link to the paper](https://arxiv.org/abs/1704.04572)
* [Implementation](https://github.com/nyu-dl/QueryReformulator)

## Key Aspect

* The underlying problem is as follows: when the end user makes a query to a search engine, the engine often relies on word matching techniques to perform retrieval. This means relevant documents could be missed if there is no exactly matching words between the query and the document.
* This problem can be handled at two levels: First, the search engine itself takes care of query semantics. Alternatively, we assume the search engine to be dumb and instead have a system in place that can improve the original queries (automatic query reformulation).
* The paper takes the latter approach and expands the original query by adding terms from the set of retrieved documents (pseudo relevance feedback).

## Datasets

* TREC - Complex Answer Retrieval (TREC-CAR)
* Jeopardy Q&A dataset
* Microsoft Academic (MSA) dataset - created by the authors using papers crawled from Microsoft Academic API

## Framework

* Query Reformulation task is modeled as an RL problem where:
    * Environment is the search engine.
    * Actions are whether a word is to be added to the query or not and if yes, then what word is added.
    * Reward is the retrieval accuracy.

* The input to the system is a query q<sub>0</sub> consisting of a sequence of words w<sub>1</sub>, ..., w<sub>n</sub> and a candidate term t<sub>i</sub> with some context words. 
* Candidate terms are all the terms that appear in the original query and the documents retrieved using the query.
* The words are mapped to vectors and then a fixed size representation is obtained for the sequence using CNN's or RNNs.
* Similarly, a representation is obtained for the candidate words by feeding them and their context words to the CNN or RNNs.
* Finally, a sigmoidal score is computed for all the candidate words.
* An RNN sequentially applies this model to emit query words till an end token is emitted.
* Vocabulary is used only from the extracted documents and not the entire vocabulary set, to keep the inference fast.

## Training

* The model is trained using REINFORCE algorithm which minimizes the *C<sub>a</sub> = (R − R~) \* sum(log(P(t\|q))) where R~ is the baseline.*
* Value network minimises *C<sub>b</sub> = &\alpha(\|\|R-R~\|\|<sup>2</sup>)*
* *C<sub>a</sub>* and *C<sub>b</sub>* are minimised using SGD.
* An entropy regulation term is added to prevent the probability distribution from reaching the peak.

## Experiments

### Baseline Methods

* **Raw** - Original query is fed to the search engine without any modification.

* **Pseudo-Relevance Feedback (PRF-TFIDF)** - The query is expanded using the top-N TF-IDF terms.

* **PRF-Relevance Model (PRF-RM)** - Probability of adding token *t* to the query *q0* is given by *P(t\|q0) = (1 − λ)P′(t\|q0) + λ sum (P(d)P(t\|d)P(q0\|d))*

### Proposed Methods

* **Supervised Learning**
    * Assumes that the query words contribute indepently to the query retrival performace. (Too strong an assumption).
    * A term is marked as relevant if *(R(new_query) - R(old_query))/R(old_query) > 0.005*

* **Reinforcement Learning**
    * RL-RNN/CNN - RL Framework + RNN/CNN to encode the input features. 
    * RL-RNN-SEQ - Add a sequential generator.

* **Metrics**
    * Recall@K
    * Precision@K
    * Mean Average Precision@K

* **Reward** - The paper uses Recall@K as a reward when training the RL-based models with the argument that the "metric has shown to be effective in improving the other metrics as well", without any justification though.

* **SL-Oracle** - classifier that perfectly selects terms that will increase performance based on the supervised learning approach.

* **RL-Oracle** - Produces a conservative upper-bound for the performance of the RL Agent. It splits the test data into N subsets and trains an RL agent for each subset. Then, the reward is averaged over all the N subsets.

## Observations

* Reformulation based methods > original query
* RL methods > Supervised methods > unsupervised methods
* RL-RNN-SEQ performs slightly worse than RL-RNN but is much faster (as it produces shorter queries).
* RL-based model benefits from more candidate terms while the classical PRF method quickly saturates.

## Comments

* Interestingly, for each raw query, they carried out the reformulation step just once and not multiple times. The number of times a query is reformulated could also have become a part of the RL framework.