---
layout: post
title: Measuring abstract reasoning in neural networks
comments: True
excerpt: 
tags: ['2018', 'ICLR 2019', 'Relation Learning', 'Relational Learning', AI, ICLR]

---

## Introduction

* The paper proposes a dataset to diagnose the abstract reasoning capabilities of learning systems.

* The paper shows that a variant of the relational networks, explicitly designed for abstract reasoning, outperforms models like ResNets.

* [Link to the paper](http://proceedings.mlr.press/v80/santoro18a/santoro18a.pdf)

## Idea

* Visual reasoning tasks, that are inspired by the human IQ test, are used to evaluate the models in terms of generalization.

* Let's say that we want to test if the model understands the abstract notion of "increasing". We could train the model on data that captures the notion of "increasing", in terms of say increasing size (or quantities) of objects and then test it on a dataset where the notion is expressed in terms of increasing intensity of color.

* The dataset is then used to evaluate if the models can find any solution to such abstract reasoning tasks and how well they generalize when the abstract content is specifically controlled.

## Dataset

### Raven's Progressive Matrics (RPMs):

* Consists of an incomplete 3x3 matrix of images where the missing image needs to be filled in, typically by choosing from a set of candidate images.

* As such, it is possible to justify multiple answers to be correct though, in practice, the right answer is the one with the simplest explanation.

### Procedurally Generated Matrices (PGMs)

* Generating RPM like matrices procedurally by building an abstract structure for matrices.

* The abstract structure *S* consists of 3 components: (i) Relation types (*R*), (ii) Object types (*O*) and (iii) Attribute types (*A*). ie *S = {(r, o, a) | r in R, o in O and a in A}*.

* This can be read as: "Structure *S* is instantiated on attribute *a* of object *o* and exhibits the relation *r*". For example, *S* is instantiated on "color" of object "shape" and exhibits the relation "increasing".

* In general, the structure could be made of more than one such tuple and more are the tuples, harder is the task.

* Given the structure, sample values *v* for each attribute *a* while conforming with the relation *r*. For example, if the attribute is "color" and the relation is "increasing", the intensity of color must increase.

* The resulting structure is rendered as pixels.

## Test for Generalization

* The paper tests for the following generalization scenarios:

* Neutral: The structure of the training and test data can contain any tuple.

* Interpolation: The training data contains even-indexed members of the attribute values while the test data contains odd-indexed members of the attribute values.

* Extrapolation: The training data contains first-half of the attribute values while the test data contains the second-half of the attribute values.

* Heldout attribute: Training data contains no tuples with (o = shape, a = color) or (o = line, a = type).

* Heldout triples: Out of 29 possible triples, 7 are held out from training and only used during testing.

* Heldout pair-of-triples: Out of 400 possible sets of pair of triples, 40 were held out and used only during testing.

* Heldout pair-of-triples: Out of 400 possible sets of pair of triples, 40 were held out and used only during testing.

* Heldout attribute pair: Out of 20 (unordered) variable attribute pairs, 4 were held out and used only during testing.

## Models

* **Input**: 8 context panels (from the 3x3) matrix where the last panel needs to be filled.

* CNN-MLP - 4 layer CNN with batchnorm and ReLU.

* ResNet - ResNet-50 (as it perfomed better than ResNet-101 and ResNet-152).

* LSTM

* Wild Relation Network (WReN) - A CNN model encodes the 8 panels and the candidate answers and feeds them as input to a relational network.

* Context-blind ResNet - ResNet network without the context (or the 8 input panels).

## Results

* WReN model outperforms the other models on the Neutral setup.

* Models have a harder time differentiating between size than quantity.

* WRen is the best performing models in all the setups and rest of the discussion only applies to that model.

* Generalisation is easy in the context of interpolation while worst in case of extrapolation, hinting at the limited generalization capability of the models.

## Auxiliary Training

* The model is also trained to predict the relevant relation, object and attribute types using the meta-targets that encode this information.

* The auxiliary training helps in all the cases. Further, the model's accuracy on the main task is where in the cases where it solves the auxiliary tasks well.

## Key Takeaway

* For abstract visual reasoning tasks, the choice of models can make a large difference, the case in consideration of ResNets vs Relational Networks.

* Using auxiliary loss that encourages the model to "explain" its reasoning (in this case by predicting the attributes, relations, etc) helps to improve the performance on the main task as well.

* Given that the challenge is motivated by tasks used to measure human IQ, it would have been interesting to get an estimate of human performance on at least a subset of this dataset.