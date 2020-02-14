---
layout: post
title: Quantifying Generalization in Reinforcement Learning
comments: True
excerpt: 
tags: ['2018', 'Deep Reinforcement Learning', 'ICML 2019', 'Evaluating Generalization', 'Reinforcement Learning',  AI, DRL, Environment, Evaluation, ICML, Generalization, RL]

---

## Introduction

* The paper introduces a new, procedurally generated environment called as CoinRun that is designed to benchmark the generalization capabilities of RL algorithms.

* The paper reports that deep convolutional architectures and techniques like L2 regularization, batch norm, etc (which were proposed in the context of generalization in supervised learning) are also useful for RL.

* [Link to the paper](https://arxiv.org/abs/1812.02341)

## CoinRun Environment

* CoinRun is made of multiple levels.

* In each level, the agent spawns on the far left side and needs to collect a single coin that lies on the far right side.

* There are many obstacles in between and colliding with an obstacle leads to agent's death.

* Each episode extends for a maximum for 1000 steps.

* CoinRun is designed such that given sufficient training time and levels, a near-optimal policy can be learned for all the levels.

## Generalization

* Generalization can be measure by training an agent on a given set of training tasks and evaluating on an unseen set of test tasks.

* 9 agents are trained to play CoinRun, on different training sets (each with a different number of levels).

* The first 8 agents are trained on sets of size 100 to 16000 levels while the last agent is trained on an unbounded set of levels.

* Training a model on an unbounded set of levels provides a good proxy for the train-to-test generalization performance.

## Evaluating Architectures

* Two convolutional architectures (of different sizes) are compared:

    * Nature-CNN: The CNN architecture used in the [Deep Q Network](https://web.stanford.edu/class/psych209/Readings/MnihEtAlHassibis15NatureControlDeepRL.pdf). This is the smaller network among the two models.

    * IMPALA-CNN: The CNN architecture used in the [Imapla architecture](https://arxiv.org/abs/1802.01561).

* IMPALA-CNN agent always outperforms the Nature-CNN agent indicating that larger architecture has more capacity for generalization. But increasing the network size beyond a limit gives diminishing returns.

## Evaluating Regularization

* While both L2 regularization and Dropout helps to improve generalization, L2 regularization is more impactful.

* A domain randomization/data augmentation approach is tested where rectangular regions of different sizes are masked and assigned a random color. This approach seems to improve performance.

* Batch Normalization helps to improve performance as well.

* Environment stochasticity is introduced by using sticky actions while policy stochasticity is introduced by controlling the entropy bonus. Both these forms of stochasticity boost performance.

* While combining different regularization methods help, the gains are only marginally better than using just 1 regularization approach. This suggests that these different approaches induce similar generalization properties.

## Additional Environments

* Two additional environments are also considered to verify the high degree of overfitting observed in the CoinRun environment:

    * CoinRun-Platforms: 

        * Unlike CoinRun, each episode can have multiple coins and the time limit is 0increased to 1000 steps.

        * Levels are larger as well so the agent might need to backtrack their steps.

    * RandomMazes:

        * Partially observed environment with square mazes of dimensions 3x3 to 25x25.

        * Timelimit of 500 steps

    
* Overfitting is observed for both these environments as well.