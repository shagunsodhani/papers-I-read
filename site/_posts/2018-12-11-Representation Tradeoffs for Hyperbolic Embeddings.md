---
layout: post
title: Representation Tradeoffs for Hyperbolic Embeddings
comments: True
excerpt: 
tags: ['2018', 'ICML 2018', 'Hyperbolic Embedding', 'Hyperboloid Model', 'Graph Representation', 'Poincare Ball Model', AI, Embedding, Graph]
---

## Introduction

* The paper describes a combinatorial approach to embed trees into hyperbolic spaces without performing optimization.

* The resulting mechanism is analyzed to obtain dimensionality-precision tradeoffs.

* To embed any metric spaces in the hyperbolic spaces, a hyperbolic generalization of the multidimensional scaling (h-MDS) is proposed.

* [Link to the paper](https://arxiv.org/abs/1804.03329)

## Preliminaries

* Hyperbolic Spaces

    * Have the "tree" like property ie the shortest path between a pair of points is almost the same as the path through the origin.

    * Generally, Poincare ball model is used given its advantages like conformity to the Euclidean spaces.

* Fidelity Measures

    * Mean Average Precision - MAP

        * A local metric that ranks between distances of the immediate neighbors.

    * Distortion

        * A global metric that depends on the underlying distances and not just the local relationship between distances.

## Combinatorial Construction for embedding hierarchies into Hyperbolic spaces

* Embed the given graph *G = (V, E)* into a tree *T*.

* Embed the tree *T* into the poincare ball *H<sub>d</sub>* of dimensionality *d*.

### Sarkar's construction to embed points in a 2-d Poincare ball

* Consider two points *a* and *b* (from the tree) where *b* is the parent of *a*. 

* Assume that *a* is embedded as *f(a)* and *b* is embedded as *f(b)* and the children of *a* needs to be embedded.

* Reflect *f(a)* and *f(b)* across a geodesic such that *f(a)* is mapped to 0 (origin) while *f(b)* is mapped to some new point *z*.

* Children of *a* are placed at points *y<sub>i</sub>* which are equally placed around a circle of radius *(e<sup>r</sup> - 1) / (e<sup>r</sup> + 1)* and maximally seperated from *z*, where *r* is the scaling factor.

* Then all the points are reflected back across the geodesic so that all children are at a distance *r* from *f(a)*.

* To embed the tree itself, place the root node at the origin, place its children around it in a circle, then place their children and so on.

* In this construct, precision scales logarithmically with the degree of the tree but linearly with the maximum path length.

### *d*-dimensional hyperbolic spaces

* In the *d*-dimensional space, the points are embedded into hyperspheres (instead of circles).

* The number of children node that can be placed for a particular angle grows with the dimension.

* Increasing dimension helps with bushy trees (with high node degree).

## Hyperbolic multidimensional scaling (h-MDS)

* Given the pairwise distance from a set of points in the hyperbolic space, how to recover the points?

* The corresponding problem in the Euclidean space is solved using MDS.

* A variant of MDS called as h-MDS is proposed.

* MDS makes a centering assumption that points have 0 mean. In h-MDS, a new mean (called as the pseudo-Euclidean mean) is introduced to enable recovery via matrix factorization.

* Instead of the Poincare model, the hyperboloid model is used (though the points can be mapped back and forth).

### pseudo-Euclidean Mean

* A set of points can always be centered without affecting their pairwise distance by simply finding their mean and sending it to 0 via isometry

### Recovery via matrix factorization

* Given the pairwise distances, a new matrix *Y* is constructed by applying *cosh* on the pairwise distances.

* Running PCA on *-Y* recovers X up to rotation.

## Dimensionality Reduction with PGA (Principal Geodesic Analysis)

* PGA is the counterpart of PCA in the hyperbolic spaces.

* First the *Karcher* mean of the given points is computed.

* All points *x<sub>i</sub>* are reflected so that their mean is 0 in the Poincare disk model.

* Combining that with Euclidean reflection formula and hyperbolic metrics leads to a non-convex loss function which can be optimized using gradient descent algorithm.

## Experiments

* Datasets
    
    * Trees: fully balanced and phylogenic trees expressing genetic heritage.
    * Tree-like hierarchy: WordNet hypernym and graph of Ph.D. advisor-advisee relationships.
    * No-tree like disease relationships, proteins interactions etc

* Results
    
    * Combinatorial construction outperforms approaches based on optimization in terms of both MAP and distortion.
    * eg on WordNet, the combinatorial approach achieves a MAP of 0.989 with just 2 dimensions while the previous best was 0.87 with 200 dimensions.
    