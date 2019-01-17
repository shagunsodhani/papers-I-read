---
layout: post
title: Hierarchical RL Using an Ensemble of Proprioceptive Periodic Policies
comments: True
excerpt: 
tags: ['2019', 'Hierarchical RL', 'ICLR 2019', AI, ICLR, Mujoco, RL]
---

## Introduction

* The paper proposes a simple and robust approach for hierarchically training an agent in the sparse reward setup.

* The broad idea is to train low-level primitives that are sufficiently diverse (so that they can be composed for solving higher level tasks) and to train a high level primitive that learns to combine these primitives for any given downstream task.

* [Link to the paper](https://openreview.net/forum?id=SJz1x20cFQ)

## Approach

* The state can be divided into two components: proprioceptive states s<sup>p</sup> (measurement of agent's own body that can be directly controlled by the agent) and the external states s<sup>e</sup>/

### Low-Level Policy Training

* Low-level policies should be:

    * Diverse: should cover all the skills that the agent might have to perform.
    * Effective: can make significant changes to the environment.
    * Controllable: easy for high-level policies to use and control

* For the low-level policy, the per-time step reward is directly proportional to change in the external state. The same reward is used for all the agents and environments(except regulated with environment specific controls and survival rewards).

### Phase conditioned policies

* Good movement policies are expected to be at least roughly periodic and phase input (or time index) is used to achieve periodicity.

* Phase conditioned policy (=f(s<sup>p</sup>, &phi;)) where &phi; = {0, 1, ..., k-1} is the phase index.

* At each timestep *t*, the model receives observation s<sup>p</sup> and phase index &phi; = t%k. The phase index is represented by a vector b<sub>&phi;</sub>.

* For phase conditioned policies, the agent state and actions are encouraged to be cyclic with the help of a cyclic loss.

## Experiments

* Environments: Ant and Humanoid from Mujoco.

* Low-level control:

    * Using phase-conditioning is helpful when training low-level primitives.

* High-level control:

    * Cross Maze Environment with fixed goals

        * 3 goals along 3 paths

        * Proposed method converges faster and to a smaller final distance to the goal showing that it is both efficient and consistent (with smaller variance across random seeds).

    * Random Goal Maze

        * The goal is randomly drawn from a set of goals.

        * "Cross" (shaped) maze and "skull" (shaped) mazes are considered.

        * Even with velocity rewards and pretraining on low-level objectives (which can be thought of as exploration bonuses), the baseline fails to get close to the goal locations while the proposed model reach the goal most of the times.

        * The main results are reported using PPO though repeating the experiments with A2C and DQN show that the idea is fairly robust.

        * The paper reported that in their experiments, finetuning the lower level primitives did not help much though it might not be the case of other environments.