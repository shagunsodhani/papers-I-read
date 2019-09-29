---
layout: post
title: PHYRE - A New Benchmark for Physical Reasoning
comments: True
excerpt: 
tags: ['2019', 'Physical Reasoning', AI, Benchmark, Dataset, Physics, Reasoning]

---

## Introduction

* The paper proposes the PHYRE (PHYsical REasoning) benchmark - consisting of classic mechanical puzzles in 2D physical environments - as a means to evaluate the physical reasoning ability of machine learning models.

* [Link to the paper](https://arxiv.org/abs/1908.05656)

## Environment

* 2D world that obeys Newtonian mechanics.

* Gravitational force + Friction.

* Non-deformable objects that can be static (ie fixed) or dynamic (ie can move and are affected by collisions etc).

## Task

* The learning agent starts in some initial world state (ie configuration of objects).

* Goal is described in the form of (`subject`, `relation`, `object`) where the agent's task is to satisfy the `relation` between the `subject` and the `object`.

* Currently, only the "touch" `relation` is supported.

## Setup

* The learning agent has to take a single action - placing one or more new dynamic objects in the world.

* A simulator is run on the new configuration (for a fixed amount of time) to check if the goal condition is satisfied.

* At the end of the simulation, a binary reward and intermediate observations (collected as the simulator executes) are provided to the learning agent.

* These observations are 256\*256 grids where each grid cell can take 1 of the 7 values (denoting different types of objects).

* Since only one relation supported currently, the color is sufficient to encode the goal.

## Benchmark Tiers

* Two benchmark tiers are provided where each tier comprises of a combination of:
    
    * a predefined set of all the actions that the agent is allowed to perform.

    * set of tasks that can be solved by at least one action from the allowed action set.

* **PHYRE-B** - The agent is allowed to place a single (ball of any radii) at any valid location.

* **PHYRE-2B** - The agent is allowed to place 2 balls at any valid pair of locations.

* Each of the two tiers has 25 task templates where each template comprises of variants of a single task (same goal but different initial conditions).

## Evaluation

* Two evaluation setups are considered:

    * **within-template** where the agent is trained on some tasks in a template and evaluated on a set of held-out tasks from the same template.

    * **cross-template** where the agent is evaluated on tasks from a different template.

* In the training phase, the model has access to the simulator (but not to the correct solution). So the model could learn an action-prediction model or forward dynamics model or both.

* In the testing phase, the model can query the simulator only a few times. Each query provides it with the binary reward and the intermediate observations.

## Performance Measure

* The emphasis is on solving more tasks (in few queries) during the test phase.

* This requirement is captured using a metric called AUCCESS.

* In general, the tasks in PHYRE-2B are harder than tasks in PHYRE-B.

## Baseline Agents

* Random Agent - Randomly samples actions

* Non-parametric agent (MEM) - generates R actions at random and uses the simulator to check how many tasks can be solved using these R random actions. During testing, try the R actions in the decreasing order of the number of tasks they solve.

* Non-parametric agent with online learning (MEM-O) - Variant of MEM where an online adaptation step is performed during test time (to update the rank of the actions).

* Deep Q Networks with an action encoder, observation encoder and fusion model (combine action and observation representation).

* DQN with online learning (DQN-0): Variant of DQN with online updates (during the test phase).

* Contextual bandits.

* Policy learning approaches like PPO and A2C.

## Observations

* Both Contextual bandits and policy-based approaches show poor training stability.

* The best agent, DQN-O, reaches AUCCESS of 56.2\% on PHYRE-B and 39.26\% on PHYRE-2B. In general, agents with online adaptation perform better.

* The tasks are designed such that 100000 attempts are sufficient to solve 100\% of tasks in PHYRE-B and 95\% of tasks in PHYRE-2B.

* Even though only two tiers are provided right now, the benchmark is readily extensible and new tasks can be added in the future.