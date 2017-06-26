---
layout: post
title: Two/Too Simple Adaptations of Word2Vec for Syntax Problems
comments: True
excerpt: The paper proposes an attention based mechanism to decompose the problem of Natural Language Inference (NLI) into parallelizable subproblems
tags: ['2015', 'ACL 2015', ACL, AI, Embedding, NLP, Word Vectors]
---

* The paper proposes two variants of Word2Vec model so that it may account for syntactic properties of words and perform better on syntactic tasks like POS tagging and dependency parsing.
* [Link to the paper](http://www.cs.cmu.edu/~lingwang/papers/naacl2015.pdf)
* In the original Skip-Gram setting, the model predicts the *2c* words in the context window (*c* is the size of the context window). But it uses the same set of parameters whether predicting the word next to the centre word or the word farthest away, thus losing all information about the word order.
* Similarly, the CBOW (Continuous Bas Of Words) model just adds the embedding of all the surrounding words thereby losing the word order information.
* The paper proposes to use a set of *2c* matrices each for a different word in the context window for both Skip-Gram and CBOW models. 
* This simple trick allows for accounting of syntactic properties in the word vectors and improves the performance of dependency parsing task and POS tagging.
* The downside of using this is that now the model has far more parameters than before which increases the training time and needs a large enough corpus to avoid sparse representation.