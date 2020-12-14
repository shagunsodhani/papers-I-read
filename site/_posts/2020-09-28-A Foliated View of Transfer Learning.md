---
layout: post
title: A Foliated View of Transfer Learning
comments: True
excerpt: 
tags: ['2020', 'Transfer Learning', AI, Geometry, Theory]


---

## Introduction

* The paper presents a formalism for transfer learning, offers a definition of relatedness between tasks, and proposes foliations as a mathematical framework to represent the relationship between tasks.

* [Link to the paper](https://arxiv.org/abs/2008.00546)


## Summary

* The term *representation* denotes a mechanism for *describing* and *realizing* abstract objects, thus allowing manipulation and reasoning about the objects. This description goes beyond the usual meaning (in deep learning), where *representation* denotes some useful information about data.

* *Relatedness* describes *what* changes between tasks. Consider a set of transformations (or functions) that convert one task to another. A *relationship* between two tasks is an element of this transformation set.

* Given a transformation set, one can define a *set of related tasks*, which is the set of all the tasks that can be transformed into each other using the functions from the given transformation set. This set of tasks is an equivalence class, and the transformation set is the equivalence relationship.

* Given two related tasks *t1* and *t2*, denote the corresponding models (trained on those tasks) as *m1* and *m2*. One can assume that *m1* and *m2* are related in the same way as *t1* and *t2* (equivariance).

* Now, given a set of transformations, one can partition the space of continuous functions into non-overlapping spaces, which describe a set of related tasks. These spaces are referred to as the *parallel spaces* or *transfer spaces*.

* The parallel space represents a lower dimension than the original space. So knowing which parallel space a model lies on can make it easier to find it. This is the primary motivation behind transfer learning - knowing the relationship between tasks can make it easier to find a solution to new tasks.

* Another way of partitioning the set of transformations is to use tessellation (e.g., Voronoi diagrams). Tasks in the same partition are similar to each other as compared to a task from another partition.

* Two tasks are defined as *similar* if the distance between them (under some distance metric) is small. 

* Similarity is a *geometric* notion, while relatedness is a *transformative* notion. Parallelized space is to relatedness what tessellation is to similarity.

* The distinction between similarity and relatedness is quite nuanced, and the authors provide several examples to differentiate between them. 

* Similarity can only be measured in terms of a reference element (similar to what). For example, when one finetunes a pre-trained model on a new task, one assumes that the model's pretraining task is similar to the current task.

* Given a set (say *T*), a *quantity* (a function that maps elemenets of *T* to a *k* dimensional vector) is said to be *invariant* with respect to a transformation *p* (defined on *T*) if *q(f) = q(p(f))* ie the value of *f* (belonging to *T*) does not change if *f* is transformed by *p*.

* If one assumes that the set of transformations is a group, specifically a Lie group whose action on the set of tasks is locally free and regular, then one can define a parallel partitioning of the space of tasks and the space of models.

* One can develop a hierarchial categorization scheme for the set of all considered tasks using the invariant quantities.

* One can consider the space of tasks and models to be smooth manifolds as manifolds naturally give a notion of representation and transformations between them.

* A manifold is a topological space that can be locally mapped to a Euclidean space using coordinate charts. One can define regular foliation by choosing charts that satisfy certain conditions. In that case, the manifold has immersed, connected, non-intersecting submanifolds called leaves.

* The charts (that satisfies those conditions) give a set of rectified coordinates, where the notions of "which leaf a point is on" and "where on the leaf it is" are clearly separated.

* Thus, foliation can provide the theoretical tools to work with parallel spaces.

* How can the foliations be incorporated into theory and solutions for transfer learning is left aa future work.