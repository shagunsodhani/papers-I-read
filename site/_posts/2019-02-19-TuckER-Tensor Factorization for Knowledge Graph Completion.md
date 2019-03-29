---
layout: post
title: TuckER - Tensor Factorization for Knowledge Graph Completion
comments: True
excerpt: 
tags: ['2019', 'Graph Representation', 'Linear Algebra', 'Linear Model', 'Matrix Factorization', 'Tucker Decomposition', AI, Embedding, Factorization, Graph, Matrix]
---

## Introduction

* TuckER is a simple, yet powerful linear model that uses Tucker decomposition for the task of link prediction in knowledge graphs.

* [Paper](https://arxiv.org/abs/1901.09590)

* [Implementation](https://github.com/ibalazevic/TuckER)

## Knowledge Graph as a Tensor

* Let E be the set of all the entities and R be the set of all the relations in a given knowledge graph (KG).

* The KG can be represented as a list of triples of the form (source entity, relation, object entity) or (e<sub>s</sub>, r, e<sub>o</sub>).

* The list of triples can be represented as a third-order tensor (of binary values) where each element corresponds to a triple and each element's value corresponds to ether that element is present in the KG or not.

* The link prediction task can be formulated as - given a set of all triples, learn a scoring function that assigns a score to each triple. The score indicates whether the triple is actually present in the KG or not.

## TuckER Decomposition

* Tucker decomposition factorizes a tensor into a set of factor matrices and a smaller core tensor.

* In the specific case of three-mode tensors (alternate representation of a KG), the given original tensor **X** (of shape *IxJxK*) can be factorized into a core tensor **W** (of shape *PxQxR*) and 3 factor matrics - **A** (of shape *IxP*), **B** (of shape *JxQ*) and **C** (of shape *KxR*) such that **X** is approximately **W** x<sub>1</sub> **A** x<sub>2</sub> **B** x<sub>3</sub> **C**, where X<sub>n</sub> denotes the tensor product along the nth mode.

* Generally, *P, Q, R* are smaller than *I, J K* (respectively) and **W** can be seen as a compressed version of **X**.

## TuckER Decomposition for Link Prediction

* Two embedding matrics are used for embedding the entities and the relations respectively.

* Entity embedding matrix **E** is shared for both subject and the object ie **E** = **A** = **B**.

* The scoring function is gives as **W** x<sub>1</sub> **e<sub>s</sub>** x<sub>2</sub> **w<sub>r</sub>** x<sub>3</sub> **e<sub>0</sub>** where **e<sub>s</sub>**, **w<sub>r</sub>** and **e<sub>o</sub>** are the embedding vectors corresonding to e<sub>s</sub>, e<sub>r</sub> and e<sub>o</sub> respectively. Note that both the core tensor and the factor matrices are to be learnt.

* Model is trained with the standard negative log-likelihood loss given as (for one triple):  y * log(p) + (1-y) * log(1-p)

* To speed up training and increase accuracy, 1-N scoring is used. A given (e<sub>s</sub>, r) is simultaneously scored for all the entities using the local-closed world assumption (knowledge graph is only locally complete). 

* Handling asymmetric relations is straightforward by learning a relation embedding alongside a relation-agnostic core tensor which enables knowledge sharing across relations.

## Theoretical Analysis

* One important consideration would be the expressive power of TuckER models, especially in relation to other models like ComplEx and SimplE.

* It can be shown the TuckER is fully expressive ie give any ground truth over E and R, there exists a TuckER model which can perfectly represent the data - using 1-hot entity and relation embedding.

* For full expressiveness, dimensionality of entity (relation) is n<sub>E</sub> (n<sub>R</sub>) where n<sub>E</sub> (n<sub>R</sub>) are the number of entities (relations). In comparsion, the required dimensionality for ComplEx is n<sub>E</sub> * n<sub>R</sub> (for both entity and relations) and for SimplE, it is min(<sub>E</sub> * n<sub>R</sub>, number of facts + 1) (for both entity and relations).

* Many existing models like RESCAL, DistMult, ComplEx, SimplE etc can be seen as special cases of TuckER.

## Experiments

### Datasets

* FB15k, FB15k-237, WN18, WN18RR

* The max number of entities is around 41K and max number of relations is around 1.3K

### Implementation

* BatchNorm, Dropout and Learning rate decay are used.

### Metrics

* Mean Reciprocal Rank (MRR) - the average of the inverse of mean rank assigned to the true triple overall n<sub>e</sub> generated triples.

* hits@k (k = 1, 3, 10) - percentage of times the true triple is ranked in the top k of the n<sub>e</sub> generated triples.

* Higher is better for both the metrics.

### Results

* TuckER outperforms all the baseline models on all but one task.

* Dropout is an important factor with higher dropout rates (0, 3, 0.4, 0.5) needed for datasets with fewer training examples per relation (hence more prone to overfitting).

* TuckER improves performance more significantly when the number of relations is large.

* Even with lower embedding dimensions, TuckER's performance does not deteriorate as much as other models.