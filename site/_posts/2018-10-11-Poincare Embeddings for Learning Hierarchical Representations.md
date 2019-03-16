---
layout: post
title: Poincaré Embeddings for Learning Hierarchical Representations
comments: True
excerpt: 
tags: ['2017', 'Hyperbolic Embedding', 'Graph Representation', 'Natural Language Processing', 'Poincare Ball Model', AI, Embedding, Graph, NLP, Tree]
---

## Introduction

* Much of the work in representation leaning uses Euclidean vector spaces to embed datapoints (like words, nodes, entities etc).

* This approach is not effective when data has a (latent) hierarchical structure.

* The paper proposes to compute the embeddings in the hyperbolic space so as to preserve both the similarity and structure information.

* [Link to the paper](https://arxiv.org/pdf/1705.08039.pdf)

## Hyperbolic Geometry

* Hyperbolic spaces are spaces with a constant negative curvature while Euclidean spaces have zero curvature.

* The hyperbolic disc area and circle length increase exponentially with the radius r while in Euclidean space, it increases quadratically and linearly respectively.

* This makes the hyperbolic space more suitable for embedding tree-like structures where the number of nodes increases as we move away from the root.

* Hyperbolic spaces can be thought of as the continuous version of trees and trees can be thought of as the discrete version of hyperbolic spaces.

## Poincare Embeddings

* Poincare model is one of the several possible models of the hyperbolic space and is considered here as it is more amenable to gradient-based optimisation.

* Distance between 2 pints change smoothly and is symmetric. Thus the hierarchical organisation only depends on the distance from the origin which makes the model applicable in settings where the hierarchical structure needs to be inferred from the data.

* Eventually the norm of a point represents its hierarchy and distance between the points represents similarity.

## Optimization

* RSGD (Riemannian SGD) method is used.
* Riemannian gradients can be computed from the Euclidean gradients by rescaling with the inverse of the Poincare ball metric tensor.
* The embeddings are constrained to be within the Poincare ball by projection operation which normalizes the magnitude of embeddings to be 1.

## Training Details

* Initializing the embeddings close to 0 (by sampling uniformly from (-0.001, 0.001)) helps.
* The model is trained for an initial burn-out period of 10 epochs with 0.1 times the learning rate so as to find a better initial angular layout.

## Evaluation

* Embedding taxonomy for wordnet task

    * Setup

        * Reconstruction
        * Link Prediction

    * The input data is a collection of a pair of words (u, v) which are related to each other. 

    * For each word pair, 10 negative samples of the form (u, v') are sampled and the training procedure uses a soft ranking loss that aims to bring the related objects closer together.

* Network Embedding

    * Baselines

        * Euclidean Embeddings
        * Translational Embedding where a relation vector corresponding to the edge type is also learnt.

    * Datasets

        * ASTROPH
        * CONDMAT
        * GRQC
        * HEPPH

*    Lexical Entailment

    * Hyperlex - Gold standard to evaluate how well the semantics models capture lexical entailment on a scale of [0, 10].

    * The key takeaway is that for all the datasets/setups, hyperbolic embeddings give a performance benefit when the embedding dimension is small.

## Challenges

* Hyperbolic embeddings are not suitable for all the datasets. Eg if the dataset is not tree-like or has cycles.

* Hyperbolic embeddings are difficult to optimize as each operation needs to be modified to be usable in the hyperbolic space.