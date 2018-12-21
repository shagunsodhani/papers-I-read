---
layout: post
title: Hindsight Experience Replay
comments: True
excerpt: 
tags: ['2017', 'NIPS 2017', 'Off policy RL', 'Reinforcement Learning', 'Sample Efficient', AI, NIPS, RL]
---

## Introduction

* Hindsight Experience Replay(HER) is a sample efficient technique to learn from sparse rewards.

* [Link to the paper](https://arxiv.org/abs/1707.01495)

## Idea

* Assume a footballer misses the goal narrowly. Even though the player does not get any "reward"(in terms of goal), the player realizes that had the goal post been shifted a bit, it would have resulted in a goal(reward).

* The same intuition is applied for the RL agent - let us say that the true goal state was *g* while the agent ends up in the state *s*. 

* While the action sequence is not useful for reaching the goal state *g*, it is indeed useful for reaching state *s*. Hence the trajectory could be replayed with the goal as *s*(and not *g*).

## Technical Details

* Multi-goal policy trained using Universal Value Function Approximation (UVFA).

* Every episode starts by sampling a start state and a goal state. Each goal has a different reward function.

* Policy uses both the current state and the current goal state and leads to a state transition sequence *s<sub>1</sub>, s<sub>2</sub>,..., s<sub>n</sub>*.

* Each of these transitions *s<sub>i</sub> -> s<sub>i+1</sub>* are stored in a buffer with both the original goal and a subset of the other goals. 

* For the goal selection, following strategies are tried:

    * *Future* - goal state is the state *k* steps after observing the state transition.

    * *Final* - goal state is the final state of the current episode.

    * *Episode* - *k* random states are selected from the current episode.

    * *Randon* - *k* states are selected randomly.

* Any off-policy algorithm can be used. Specifically, DDPG is used.

## Experiments

* Robotic arm simulated using MuJoCo for *push*, *slide* and *pick and place* tasks.

* DDPG with and without HER evaluated on the 3 tasks.

* DDPG with the HER variant significantly outperforms the baseline in all the cases.