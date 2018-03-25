---
layout: post
title: The Lottery Ticket Hypothesis - Training Pruned Neural Networks
comments: True
excerpt: The paper purposes the *lottery ticket hypothesis* to explain why a larger network (than necessary) is needed during training.
tags: ['2018', "Pruning Network", AI, Hypothesis, Initialization]
---

## Introduction

* Empirical evidence indicates that at training time, the neural networks need to be of significantly larger size than necessary.

* The paper purposes a hypothesis called the *lottery ticket hypothesis* to explain this behaviour.

* The idea is the following - Successful training of a neural network depends on a *lucky* random initialization of a subcomponent of the network. Such components are referred to as *lottery tickets*.

* Larger networks are more likely to have these *lottery tickets* and hence are easier to train.

* [Link to the paper](https://arxiv.org/abs/1803.03635)

## Methodology

* Various aspects of the hypothesis are explored empirically.

* Two tasks are considered - MNIST and XOR.

* For each task, the paper considers networks of different sizes and empirically shows that larger networks are more likely to converge (or have better performance) for a fixed number of epochs as compared to the smaller networks.

* Given a large, trained network, some weights (or units) of the network are pruned and the resulting network is reset to its initial random weights. 

* The resulting network is the *lottery-ticket* in the sense that when the pruned network is trained, it is more likely to converge than an otherwise randomly initialised network of the same size. Further, it is more likely to match the original, larger network in terms of performance.

* The paper explores different aspects of this experiment: 
    
    * Pruning Strategies:
        * One-shot strategy prunes the network in one-go while the iterative strategy prunes the network iteratively.
        * Though the latter is computationally more intensive, it is more likely to find a lottery ticket. 

    * Size of the pruned network affects the speed of convergence when training the *lottery ticket*.

    * If only the architecture or only the initial weights of the *lottery ticket* are used, the resulting network tends to converge more slowly and achieves a lower level of performance.

    * This indicates that the lottery ticket depends on both the network architecture and the weight initialization.

## Discussion

* The paper includes some more interesting experiments. For instance, the distribution of the initialization in the weights that survived the pruning suggests that small weights from before training tend to remain small after training.

* One interesting experiment would be to show the performance of the pruned network before resetting its weights and retraining again. This performance should be compared with the performance of the initial large network and the performance of the *lottery ticket* after training.

* Overall, the experiments are not sufficient to conclude anything about the correctness of the hypothesis. The proposition itself is very interesting and could enhance our understanding of how the neural networks work.
