---
layout: post
title: Revisiting Fundamentals of Experience Replay
comments: True
excerpt: 
tags: ['2020', 'Deep Reinforcement Learning', 'ICML 2020', 'Off policy RL', 'Reinforcement Learning', 'Replay Buffer', AI, DRL, Empirical, ICML, RL]


---


## Introduction

* The paper presents an extensive study of the effects of experience replay in Q-learning based methods.

* It focuses explicitly on the replay capacity and replay ratio (ratio of learning updates to experience collected).

* [Link to the paper](https://arxiv.org/abs/2007.06700)

## Setup

* Replay capacity is defined as the total number of transitions stored in the replay buffer.

* Age of a transition (stored in the replay buffer) is defined as the number of gradient steps taken by the agent since the transition was stored.

* More is the replay capacity, more will be the age of the oldest transition (also referred to as the age of the oldest policy).

* More is the replay capacity, more will be the degree of "off-policyness" of the transitions in the buffer (with everything else held constant).

* Replay ratio is the number of gradient updates per environment transition. This ratio can be used as a proxy for how often the agent uses old data (vs. collecting new data) and is related to off-policyness.

* In [DQN paper](https://www.nature.com/articles/nature14236), the replay ratio is set to be 0.25.

* For experiments, a subset  (of 14 games) is selected from Atari ALE (Arcade Learning Environment) with sticky actions.

* Each experiment is repeated with three seeds.

* Rainbow is used as the base algorithm.

* Total number of gradient updates and batch size (per gradient update) are fixed for all the experiments.

* Rainbow used replay capacity of 1M and oldest policy of age 250K.

* In experiments, replay capacity varies from 0.1M to 10M ( 5 values), and the age of the oldest policy varies from 25K to 25M (4 values).

## Observations

* With the age of the oldest policy fixed, performance improves with higher replay capacity, probably due to increased state-action coverage.

* With fixed replay capacity, reducing the oldest policy's age improves performance, probably due to the reduced off-policyness of the data in the replay buffer.

* However, in some specific instances (with sparse reward, hard exploration setup), performance can drop when reducing the oldest policy's age.

* Increasing replay capacity, while keeping the replay ratio fixed, provides varying improvements and depends on the particular values of replacy capacity and replay ratio.

* The paper reports the effect of these choices for DQN as well.

* Unlike Rainbow, DQN does not improve with larger replay capacity, irrespective of whether the replay ratio or age of the oldest policy is kept fixed.

* Given that the Rainbow agent is a DQN agent with additional components, the paper explores which of these components leads to an improvement in Rainbow's performance as replay capacity increases.


## Additive Experiments

* Four new DQN variants are created by adding each of Rainbow's four components to the base DQN agent.

* DQN with n-step returns is the only variant that benefits by increased replay capacity.

* The usefulness of n-step returns is further validated by verifying that Rainbow agent without n-step returns does not benefit by increased replay capacity. While Rainbow agent without any other component benefits by the increased capacity.

* Prioritized Experience Replay does not significantly affect the performance with increased replay capacity.

* The observation that n-step returns are critical for taking advantage of larger replay sizes is surprising because the uncorrected n-step returns are theoretically not suitable for off-policy learning.

* The paper tests the limits of increasing replay capacity (with n-step returns) by performing experiments in the offline-RL setup, the agent collects a dataset of about 200M frames. These frames are used to train another agent.

* Even in this extreme setup, n-step returns improve the learning agent's performance.

## Why do n-step returns help?

* Hypothesis 1: n-step returns help to counter the increased off-policyness produced by a larger replay buffer.

	* This hypothesis does not seem to hold as keeping the oldest policy fixed or using the same contrastive factor as an n-step update does not improve the 1-step update's performance.

* Hypothesis 2: Increasing the replay buffer's capacity may reduce the variance of the n-step returns.

	* This hypothesis is evaluated by training on environments with lesser variance or by turning off the sticky actions in the atari domain.

	* While the hypothesis does explain the gains by using n-step returns to some extent, n-step gains are observed even in environments with low variance.