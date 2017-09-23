---
layout: post
title: Neural Module Networks
comments: True
excerpt: Standard VQA models benefit from the inherent bias in the structure of the world and the language of the question.
tags: ['2016', 'CVPR 2016', AI, CV, CVPR, Dataset, Neural Module Network, NLP, VQA]
---

## Introduction

* For the task of [Visual Question Answering](https://shagunsodhani.in/papers-I-read/VQA-Visual-Question-Answering), decompose a question into its linguistic substructures and train a neural network module for each substructure.
* Jointly train the modules and dynamically compose them into deep networks which can learn to answer the question.
* Start by analyzing the question and decide what logical units are needed to answer the question and what should be the relationship between them.
* The paper also introduces a new dataset for Visual Question Answering which has challenging, highly compositional questions about abstract shapes.
* [Link to the paper](https://arxiv.org/abs/1511.02799)

## Inspiration

* Questions tend to be compositional.
* Different architectures are needed for different tasks - CNNs for object detection, RNNs for counting.
* Recurrent and Recursive Neural Networks also use the idea of a different network graph for each input.

## Neural Module Network for VQA

* Training samples of form *(w, x, y)*
    * *w* - Natural Language Question
    * *x* - Images
    * *y* - Answer
* Model specified by collection of modules *{m}* and a network layout predictor *P*.
* Model instantiates a network based on *P(w)* and uses that to encode a distribution *P(y\|w, x, model_params)*

## Modules

* Find: Finds objects of interest.
* Transform: Shift regions of attention.
* Combine: Merge two attention maps into a single one.
* Describe: Map a pair of attention and input image to a distribution over the labels.
* Measure: Map attention to a distribution over the labels.

## Natural Language Question to Networks

* Map question to the layout which specifies the set of modules and connections between them.
* Assemble the final network using the layout.
* Parse the input question to obtain set of dependencies and obtain a representation similar to combinatory logic.
* eg "what is the colour of the truck?" becomes "colour(truck)"
* The symbolic representation is mapped to a layout:
    * All leaves become *find* module.
    * All internal nodes become *transform/combine* module.
    * All root nodes become *describe/measure* module.

## Answering Natural Language Question

* Final model combines output from a simple LSTM question encoder with the output of the neural module network.
* This helps in modelling the syntactic and semantic regularities of the question.

## Experiments

* Since some modules are updated more frequently than others, adaptive per weight learning rates are better.
* The paper introduces a small SHAPES datasets (64 images and 244 unique questions per image).
* Neural Module Network achieves a score of 90% on SHAPES dataset while VIS + LSTM baseline achieves an accuracy of 65.3%.
* Even on natural images (VQA dataset), the neural module network outperforms the VIS + LSTM baseline.

