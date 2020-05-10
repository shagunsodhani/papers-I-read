---
layout: post
title: Learning an SAT Solver from Single-Bit Supervision
comments: True
excerpt: 
tags: ['2018', 'Graph Neural Network', AI, Graph, GNN, SAT]
---

## Introduction

* The paper presents NeuroSAT, a message passing neural network that is trained to predict if a given SAT can be solved. As a side effect of training, the model also learns how to solve the SAT problem itself without any extra supervision.

* [Link to the paper](https://arxiv.org/abs/1802.03685)

## Background

* Given an expression in the propositional logic, the task is to predict if there exists a substitution of variables that make the expression true.

* The expression itself can be written as a conjunction of disjunctions ("and" over "or") where each conjunct is called a clause and each variable within a clause is called a literal.

* Invariants

    * The variables or clauses or literals (within the clauses) can be permuted.

    * Every occurrence of a variable can be negated.

## Model

* Given the SAT problem,  create an undirected graph of literals, their negations and the clauses they belong to. 

* Put an edge between every literal and the clause to which it belongs and another kind of edge between every literal and its negation.

* Perform message passing between nodes to obtain vector representations corresponding to each node. Specifically, first, each clause received a message from its neighbours (literals) and updates its embeddings. Then every literal receives a message from its neighbours (both literals and clauses) and updates its embeddings.

* After T iterations, the nodes vote to decide the prediction of the model as a whole.

* The model is trained end-to-end using the cross-entropy loss between logit and the true label.

* Permutation invariance is ensured by operating on the nodes and the edges in the topological order and negation invariance is ensured by treating all literals as the same.

## Decoding Satisfying Assignment

* The most interesting aspect of this work is that even though the model was trained to predict if the SAT problem can be satisfied, it is actually possible to extract the correct assignment from the classifier.

* In the early iterations, all the nodes vote "unsolvable" with low confidence. Then a few nodes start voting "solvable" and then a phase transition happens where most of the nodes start voting "solvable" with high confidence.

* The model never becomes highly confident that problem is "unsolvable" and almost never guesses "solvable" on an "unsolvable" problem. So in some sense, the model is looking for the combination of literals that actually solves the problem.

* The authors found that the 2 dimensional PCA projections of the literal embeddings are initially mixed up but become more and more linearly separable as the phase transition happens.

* Based on this insight, the authors propose to obtain cluster centres C1 and C2, partition the variables according to the cluster centres and then try assignments from both the partitions.

* This alone provides a satisfying solution in over 70% of the cases when though there is no explicit supervising signal about how to solve the problem.

* The other strengths of the paper includes

    * Generalizing to longer and more difficult SAT problems (than those seen during training).

    * Generalizing to another kind of search problems like graph colouring, clique detection etc (over small random graphs).

* The paper also reports that by adding supervising signal about which clauses in the given expression are unsatisfiable, it is possible to decode the literals which prove the "unsatisfiability" of an expression at test time. Though not a lot of details have been provided about this part and would probably be covered in the next iteration of the paper.

