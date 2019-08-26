---
layout: post
title: Abductive Commonsense Reasoning
comments: True
excerpt: 
tags: ['2019', 'Abductive Reasoning', 'Natural Language Inference', 'Natural Language Processing', AI, Dataset, NLI, NLP, Reasoning]

---

## Introduction

* The paper presents the task of abductive NLP (pronounced as *alpha NLP*) where the model needs to perform abductive reasoning.

* Abductive reasoning is the inference to the most plausible explanation. Even though it is considered to be an important component for understanding narratives, the work in this domain is sparse.

* A new dataset called as Abstractive Reasoning in narrative Text (ART) consisting of 20K narrative contexts and 200k explanations is also provided. The dataset models the task as multiple-choice questions to make the evaluation process easy. 

* [Link to the paper](https://arxiv.org/abs/1908.05739)

## Task Setup

* Given a pair of observations *O<sub>1</sub>* and *O<sub>2</sub>* and two hypothesis *h<sub>1</sub>* and *h<sub>2</sub>*, the task is to select the most plausible hypothesis.

* In general, *P(h \| O<sub>1</sub>, O<sub>2</sub>)* is propotional to *P(h \|O<sub>1</sub>)P(O<sub>2</sub>\|h, O<sub>1</sub>)*.

* Different independence assumptions can be imposed on the structure of the problem eg one assumption could be that the hypothesis is independent of the observations or the "fully connected" assumption would jointly model both the observations and the hypothesis.

## Dataset

* Along with crowdsourcing several plausible hypotheses for each observation instance pair, an adversarial filtering algorithm (AF) is used to remove weak pairs of hypothesis.

* Observation pairs are created using the [ROCStories dataset](https://aclweb.org/anthology/N16-1098) which is a collection of short, manually crafted stories of 5 sentences.

* The average word length for both the content and the hypothesis is between 8 to 9.

* To collect plausible hypothesis, the crowd workers were asked to fill in a plausible "in-between" sentence in natural language.

* Given the plausible hypothesis, the crowd workers were asked to create an implausible hypothesis by editing fewer than 6 words.

* Adversarial filtering approach from [Zellers et al.](https://aclweb.org/anthology/D18-1009) is used with BERT as the adversary. A temperature parameter is introduced to control the maximum number of instances that can be changed in each adversarial filtering iteration.

## Key Observations

* Human performance: 91.4%

* Baselines like SVM classifier, the bag-of-words classifier (using Glove) and max-pooling overt BiLSTM representation: approx 50%

* Entailment NLI baseline: 59%. This highlights the additional complexity of abductive NLI as compared to entailment NLI.

* BERT: 68.9%

* GPT: 63.1%

* Numerical and spatial knowledge-based data points are particularly hard.

* The model is more likely to fail when the narrative created by the incorrect hypothesis is plausible

