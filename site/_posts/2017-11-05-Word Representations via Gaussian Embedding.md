---
layout: post
title: Word Representations via Gaussian Embedding
comments: True
excerpt: The paper proposes to learn Gaussian function embeddings (with diagonal covariance) for the word vectors.
tags: ['2015', 'ICLR 2015', 'Representation Learning', 'Word Vectors', AI, ICLR, NLP]
---

## Introduction

* Existing word embedding models like [Skip-Gram](https://gist.github.com/shagunsodhani/176a283e2c158a75a0a6), [GloVe](https://gist.github.com/shagunsodhani/efea5a42d17e0fcf18374df8e3e4b3e8) etc map words to fixed sized vectors in a low dimensional vector space.
* This fixed point setting cannot capture uncertainty about representation.
* Further, these fixed point vectors are compared with measures like dot product and cosine similarity which are not suitable for capturing asymmetric properties like textual entailment and inclusion.
* The paper proposes to learn Gaussian function embeddings (with diagonal covariance) for the word vectors.
* This way, the words are mapped to soft regions in the embedding space which enables modeling uncertainty and asymmetric properties like inclusion and uncertainty.
* [Link to the paper](https://arxiv.org/abs/1412.6623)
* [Implementation](https://github.com/seomoz/word2gauss)

## Approach

* KL divergence is used as the asymmetric distance function for comparing the distributions.
* Unlike the Word2Vec model, the proposed model uses ranking-based loss.

### Similarity Measures used

* **Symmetric Similarity**

* For two gaussian distributions, *P<sub>i</sub>* and *P<sub>j</sub>*, compute the inner product *E(P<sub>i</sub>, P<sub>j</sub>)* as *N(0; mean<sub>i</sub> - mean<sub>j</sub>, sigma<sub>i</sub> + sigma<sub>j</sub>)*.
* Compute the gradient of *mean* and *sigma* with respect to *log(E)*.
* The resulting loss function can be interpreted as pushing the means closer which encouraging the two gaussians to be more concentrated.

* **Asymmetric Similarity**

* Use KL divergence to encode the context distribution.
* The benefit over the symmetric setting is that now entailment type relations can also be modeled.
* For example, a low KL divergence from x to y indicates that y can be encoded as x or that y "entails" x.

## Learning

* One of the two notions of similarity is chosen and max-margin is used as the loss function.
* Mean is regularized by adding a simple constraint on the L2-norm.
* For covariance matrix, the eigenvalues are constrained to lie within a hypercube. This ensures that the positive-definite property of the covariance matrix is maintained while having a constraint on the size.

## Observations

* Polysemous words have higher variance in their word embeddings as compared to specific words.
* KL divergence (with diagonal covariance) outperforms other models.
* Simple tree hierarchies can also be modeled by embedding into the Gaussian space. A Gaussian is created for each node with randomly initialized mean and the same set of embeddings is used for nodes and context.
* For word similarity benchmarks, embeddings with spherical covariance have a slight edge over embeddings with diagonal covariance and outperform the Skip-Gram model in all the cases.

## Future Work

* Use combinations of low rank and diagonal matrices for covariances.
* Improved optimisation strategies.
* Trying other distributions like Student's-t distribution.
