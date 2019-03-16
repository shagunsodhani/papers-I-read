---
layout: post
title: Linguistic Knowledge as Memory for Recurrent Neural Networks
comments: True
excerpt: 
tags: ['2017', 'Natural Language Processing', AI, Memory, NLP, QA, RNN]
---

* [Link to the paper](https://arxiv.org/abs/1703.02620)

* Training RNNs to model long term dependencies is difficult but in some cases, the information about dependencies between elements (of the sequence) may be present in the form of symbolic knowledge.

* For example, when encoding sentences, coreference, and hypernymy relations can be extracted between tokens.

* These elements(tokens) can be connected with each other with different kind of edges resulting in the graph data structure.

* One approach could be to model this knowledge(encoded in the graph) using a graph neural network (GNN).

* The authors prefer to encode the information into 2 DAGs (via topological sorting) as training the GNN could add some extra overhead.

* This results into the Memory as Acyclic Graph Encoding RNN (MAGE-RNN) architecture. Its GRU version is referred to as MAGE-GRU.

* Given an input sequence of tokens [x<sub>1</sub>, x<sub>2</sub>, ..., x<sub>T</sub>] and information about which tokens relate to other tokens, a graph G is constructed with different (possibly typed) edges.


* Given the graph *G*, two DFS orderings are computed - forward DFS and backward DFS.

* MAGE-RNN uses separate networks for accessing the forward and backward DFS orders.

* A separate hidden state is maintained for each entity type to separate memory content from addressing.

* For any DFS order (forward or backward), the representation at time *t* is given as the concatenation of representation of different edge types at that time.

* The hidden states (for different edge types at time t) are updated in the topological order using the current state of all incoming edges at x<sub>t</sub>.

* The representation of the DFS order is given as the sequence of all the previous representations.

* In some cases, elements across multiple sequences could be related to each other. In that case, the graph is decomposed into a collection of DAGs and use MAGE-GRU on the DAGs by taking one random permutation of the sequences and decomposing it into the forward and the backward graphs.

* The model is evaluated on the task of text comprehension with coreference on bAbi dataset (story based QA), LAMBADA dataset (broad context language modeling) and CNN dataset (cloze-style QA).

* MAGE-GRU was used as a replacement for GRU units in bi-directional GRUs and GA-Reader architecture.

* DAG-RNN and shared version of MAGE-GRU (with shared edge types) are the other baselines.

* For all the cases, the model with MAGE-GRU works the best.
