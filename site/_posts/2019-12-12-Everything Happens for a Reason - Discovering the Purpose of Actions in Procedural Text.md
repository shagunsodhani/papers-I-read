---
layout: post
title: Everything Happens for a Reason - Discovering the Purpose of Actions in Procedural Text
comments: True
excerpt: 
tags: ['2019', 'EMNLP 2019', 'Procedural Text', 'Relation Learning', 'Relational Learning', AI, Dataset, EMNLP, Graph, NLP, Reasoning]

---

## Introduction

* Procedural text comprehension tasks focus on modeling the effect of actions and predicting what happens next.

* But they do not consider *why* some actions need to happen before other actions.

* The paper proposes a new model called XPAD (eXPlainable Action Dependency) that considers the *purpose* of actions while predicting their effect.

* The model favors *effects* that:
   
   * explain more of actions in the text.

   * are more plausible given the context.

* An existing procedural text benchmark dataset (Propara) is expanded by adding the task of explaining actions by predicting their dependencies.

* [Link to the paper](https://arxiv.org/abs/1909.04745)

* [Link to the dataset](http://data.allenai.org/propara/)

## Setup

* Input 
   
   * Procedural (chronologically ordered text) sequence of *T* sentences.

   * List of *N* participant entities, whose state changes at some step.

* Output
   
   * State change matrix $\pi(T \times N)$ with four possible states - move, create destroy, none.

   * This matrix tracks how property changes after each step.

* Dependency Explanation Graph

   * Identify what steps are necessary to execute a given step (say *s<sub>i</sub>*) and represent this dependency in the form of a dependency explanation graph *G = <S, E>*.

   * In this graph, each node is a step and the direction of edge describes the order of dependency.

## Dependency Graph Dataset

* [Propara dataset](https://arxiv.org/abs/1805.06975) is expanded to extract the dependency graph using both heuristic and automated methods.

* The automated method is based on the coherence assumption that if step *s<sub>j</sub>* changes state of entity *e<sub>k</sub>* then *s<sub>j</sub>* is a precondition for the first subsequent step that changes the state of *e<sub>k</sub>*.

## XPAD Model

* The model is based on the ProStruct system and uses an encoder-decoder based architecture.

* Encoder
   
   * Input: Sentence *s<sub>t</sub>* and entity *e<sub>j</sub>*.

   * Sentence is encoded using the GloVe vectors and a BiLSTM model and the entity is encoded as an indicator variable.

   * The combined representation is denoted as *c<sub>tj</sub>*.

   * This representation is passed through an MLP to generate *k* logits that encode the probability of each entity *j* undergoing a state change at step *t*.

* Decoder
   
   * Beam search is performed to decode the encoder representation into the state change matrix and dependency graph using a score function that ensures global consistency.

   * Score function has two components:

     * State change score - depends on the likelihood that the selected state changes at step *t* given the text and state change history from steps *s<sub>1</sub>* to *s<sub>t-1</sub>*.

     * Dependency graph score

       * This is based on the connectivity and likelihood of the resulting dependency explanation graph. 

       * This score is used to bias the graph search towards:

         * predictions that have an identifiable purpose ie checking if a particular state change prediction leads to a connection in the dependency explanation graph.

         * graphs that are more likely according to the background knowledge to distinguish likely dependency links from the unlikely ones.

* During training, XPAD has access to the correct path (in the search space) and learns to minimize the joint loss corresponding to predicting the state change and the dependency explanation graph.

* During testing, XPAD performs beam search to predict the most likely state change and dependency explanation graph.

## Experiments

* Tasks:
   
   * State change prediction

   * Dependency explanation prediction

* Baselines:

   * [Recurrent Entity Networks](https://arxiv.org/abs/1612.03969)

   * [Query-Reduction Networks](https://arxiv.org/abs/1606.04582)

   * [ProLocal and ProGlobal](https://arxiv.org/abs/1805.06975)

   * [ProStruct](https://arxiv.org/abs/1808.10012)

* XPAD significantly outperforms all the baseline models on the dependency explanation task.

* Improvements on the state change prediction task are less significant.

* Removing dependency graph scores from XPAD leads to a drop in the F1 score.

* The paper provides an elaborate discussion on the different types of errors that the XPAD system makes.
