---
layout: post
title: Deep Reinforcement Learning and the Deadly Triad
comments: True
excerpt: 
tags: ['2018', 'Deep Reinforcement Learning', 'Empirical Advice', 'Off policy RL', 'Reinforcement Learning', AI, DRL, Empirical, RL]


---


## Introduction

* The paper investigates the practical impact of the deadly triad (function approximation, bootstrapping, and off-policy learning) in deep Q-networks (trained with experience replay).

* The deadly triad is called so because when all the three components are combined, TD learning can diverge, and value estimates can become unbounded.

* However, in practice, the component of the deadly triad has been combined successfully. An example is training DQN agents to play Atari.

* [Link to the paper](https://arxiv.org/abs/1812.02648)

## Setup

* The effect of each component of the triad can be regulated with some design choices:

	* Bootstrapping - by controlling the number of steps before bootstrapping.

	* Function approximation - by controlling the size of the neural network.

	* Off-policy learning - by controlling how data points are sampled from the replay buffer (i.e., using different prioritization approaches)

* The problem is studied in two contexts: toy example and Atari 2600 games.

* The paper makes several hypotheses about how different components may interact in the triad and evaluate these hypotheses by training DQN with different hyperparameters:
	
	* Number of steps before bootstrapping - 1, 3, 10

	* Four levels of prioritization (for sampling data from the replay buffer)

	* Bootstrap target - Q-learning, target Q-learning, inverse double Q-learning, and double Q-learning

	* Network sizes-small, medium, large and extra-large.

* Each experiment was run with three different seeds.

* The paper formulates a series of hypotheses and designs experiments to support/reject the hypotheses.


## Hypothesis 1: Combining Q learning with conventional deep RL function spaces does not commonly lead to divergence

* Rewards are clipped between -1 and 1, and the discount factor is set to 0.99. Hence, the maximum absolute action value is bound to smaller than 100. This upper bound is used soft-divergence in the value estimates.

* The paper reports that while soft-divergence does occur, the values do not become unbounded, thus supporting the hypothesis.

## Hypothesis 2: There is less divergence when correcting for overestimation bias or when bootstrapping on separate networks.

* One manifestation of bootstrapping on separate networks is target-Q learning. While using separate networks helps on Atari, it does not entirely solve the problem on the toy setup.

* One manifestation of correcting for the overestimation bias is using double Q-learning.

* In the standard form, double Q-learning benefits by bootstrapping on a separate network. To isolate the gains by using each component independently, an inverse double Q-learning update is used that does not use a separate target-network for bootstrapping.

* Experimentally, Q-learning is the most unstable while target Q-learning and double Q-learning are the most stable. This observation supports the hypothesis.

## Hypothesis 3: Longer multi-step returns will diverge easily

* This hypothesis is intuitive as the dependence on bootstrapping is reduced with multi-step returns.

* Experimental results support this hypothesis.

## Hypothesis 4: Larger, more capacity networks will diverge less easily.

* This hypothesis is based on the assumption that more flexible value function approximations may behave more like the tabular case.

* In practice, smaller networks show fewer instances of instability than the larger networks.

* The hypothesis is not supported by the experiments.

## Hypothesis 5: Stronger prioritization of updates will diverge more easily.

* This hypothesis is supported by the experiments for all the four updates.

## Effect of the deadly triad on the agent's performance

* Generally, soft-divergence correlates with poor control performance.

* For example, longer multi-step returns lead to fewer instances of instabilities and better performance.

* The trend is more interesting in terms of network capacity. Large networks tend to diverge more but also perform the best.

* While action-value estimates can grow to large values, they can recover to plausible values as training progresses.