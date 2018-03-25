---
layout: post
title: Improving Information Extraction by Acquiring External Evidence with Reinforcement Learning
comments: True
excerpt: 
tags: ['2016', 'EMNLP 2016', 'Information Retrieval', AI, EMNLP, NLP, RL]
---

## Introduction

* Information Extraction  - Given a query to be answered and an external search engine, information extraction entails the task of issuing search queries, extracting information from new sources and reconciling the extracted values till we are sufficiently confident about the extracted values.

* The paper proposes the use of Reinforcement Learning (RL) to solve this task.

* [Link to the paper](https://arxiv.org/abs/1603.07954)

* [Implementation](https://github.com/karthikncode/DeepRL-InformationExtraction)

## Key Aspect

* Use of Reinforcement Learning to resolve the ambiguity inherent in the textual documents.
* Given a query, the RL agent would use template statement to formulate the queries (to be performed on the black box search engine). It would further resolve and combine the result for the query from the set of retrieved documents.

## Datasets

* Database of Mass Shootings in the United States.
* Food Shield database of illegal food adulteration.

## Framework

* Information extraction task is modelled as a Markov Decision Process (MDP) <S, A, T, R>

* **S** - Set of all possible states
    * The state consists of:
        * Extractor's confidence in predicted entity values.
        * Context from which values are extracted.
        * Similarity between the new document (extracted just now from the search engine) and the original document accompanying the given query.

* **A** - Set of all possible actions
    * Reconciliation decision - d
        * Accept all entities values.
        * Reject all entities values.
        * Stop the current episode.

    * Query choice - q
        * Choose the next query from a set of automatically generated alternatives.

* **R** - Rewards
    * Maximise the final extraction accuracy while minimising the number of queries.

* **Q** - Queries
    * Generated using a template. 
    * The query is searched on a search engine and the top k links are retrieved.

* **Transition**
    * Start with a single source article x<sub>i</sub> and extract the initial set of entities.
    * At each timestep, the agent is given the state (s) on basis of which it chooses the action (d, q). The episode stops whenever the action is a stop action.

* Deep Q Network is used.

* Parameters are learned using SGD and RMSProp.

## Experimental Setup

### Extraction Model

* Max Entropy Classifier is used as the base extraction system. 
* First, all the words in the document are tagged as one of the entity types and the mode of these values is used to obtain the set of extracted entities.

### Baseline

* Basic Extractors
* Aggregation System which either chooses the entity value with the highest confidence or takes a majority vote over all extracted values.
* Meta-Classifier which operates over the same input state space and produces the same set of reconciliation decisions as the DQN.
* Oracle Extractor which is computed assuming perfect reconciliation and query decisions on the top of the Maxnet base extractor.

### RL Models

* RL Basic - Only reconciliation decision.
* RL Query - Only query decision with a fixed reconciliation strategy.
* RL Extract - the full system with both reconciliation and query decision.

## Result

* RL Extract obtains substantial gains eg up to 11% over Maxnet.
* Simple aggregation schemes do not handle the task well.
* In terms of reward structure, providing rewards after each step works better than a single delayed reward. 
