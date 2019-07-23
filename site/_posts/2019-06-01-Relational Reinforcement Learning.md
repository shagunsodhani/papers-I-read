---
layout: post
title: Relational Reinforcement Learning
comments: True
excerpt: 
tags: ['2018', 'Deep Reinforcement Learning', 'ICLR 2019', 'Reinforcement Learning', 'Relational Learning', AI, ICLR, RL, RRL]

---

## Introduction

* Relational Reinforcement Learning (RRL) paradigm uses relational state (and action) space and policy representation to leverage the generalization capability of relational learning for reinforcement learning.

* The paper shows that effectiveness of RRL - in terms of generalization, sample efficiency and interplay - using box-world and StarCraft II minigames.

* [Link to the paper](https://arxiv.org/abs/1806.01830).

## Architecture

* The main idea is to use neural network models that operate on structured representations and perform relational reasoning via iterated, message-passing style methods.

* Use of non-local computations using a shared function (in terms of pairwise interactions between entities) provides a better inductive bias.

* Multi-head dot product attention mechanism is used to model the pairwise interactions (with one or more attention blocks).

* Iterative computations can be used to capture higher-order interactions between entities.

* Entity extraction is based on the assumption that entities are things located at a particular point in space.

*  A CNN is used to parse the pixel space observation into *k* feature maps of size *nxn*. The *(x, y)* coordinates are concatenated to each *k-*dimensional pixel feature-vector to indicate the pixel's position in the map.

* The resulting *n<sup>2</sup> x k* matrix acts as the entity matrix.

* Actor-critic architecture (using distributed agent IMPALA) is used.

## Environment

### Box-World

* 12 x 12-pixel room with keys and boxes placed randomly.

* Agent can move in 4 directions.

* The task is to collect gems by unlocking boxes (which may contain keys to unlock other boxes).

* Each level has a unique sequence in which boxes need to be opened as opening the wrong box could make the level unsolvable.

* Difficulty of a level can be controlled using: (i) Number of boxes in the path to the goal. (ii) The number of distractor branches, (iii)  Length of distractor branches.

### StarCraft II minigames

* 9 mini games designed as specific scenarios in the Starcraft game are used.

## Results

### Box-World

* RRL agents solve over 98% of the levels while the RL agent solves less than 95% of the levels.

* Visualising the attention scores indicate that:
    
    * keys attend to locks they can unlock.
    
    * all objects attend to agent's location.
    
    * agent and gem attend to each other (and themselves).

* Generalization capacity is tested in two ways:
    
    * Performance on levels that require opening a larger sequence of boxes than it is trained on.
    
    * Performance on levels that require key-lock combinations not seen during training.

* In both the scenarios, the RRL agent significantly outperforms the RL agent.

## StarCraft

* RLL agent achieves better or equal results that the RL agent in all but one game.

* For testing generalization, the agent, that was trained for controlling two marines, was transferred on the task which requires it to control 5 marines. These results are not conclusive given the high variability.