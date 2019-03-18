---
layout: post
title: Model Primitive Hierarchical Lifelong Reinforcement Learning
comments: True
excerpt: 
tags: ['2019', 'AAMAS 2019', 'Catastrophic Forgetting', 'Continual Learning', 'Hierarchical Reinforcement Learning', 'Lifelong Learning', 'Reinforcement Learning', AAMAS, AI, CL, HRL, RL]
---

## Introduction

* The paper presents a framework that uses diverse suboptimal world models that can be used to break complex policies into simpler and modular sub-policies.

* Given a task, both the sub-policies and the controller are simultaneously learned in a bottom-up manner.

* The framework is called as Model Primitive Hierarchical Reinforcement Learning (MPHRL).

* [Link to the paper](https://arxiv.org/abs/1903.01567)

## Idea

* Instead of learning a single transition model of the environment (aka *world model*) that can model the transitions very well, it is sufficient to learn several (say *k*) suboptimal models (aka *model primitives*).

* Each *model primitive* will be good in only a small part of the state space (aka *region of specialization*).

* These *model primitives* can then be used to train a gating mechanism for selecting sub-policies to solve a given task.

* Since these *model primitives* are sub-optimal, they are not directly used with model-based RL but are used to obtain useful functional decompositions and sub-policies are trained with model-free approaches.

## Single Task Learning

* A gating controller is trained to choose the sub-policy whose *model primitive* makes the best prediction.

* This requires modeling *p(M<sub>k</sub> \| s<sub>t</sub>, a<sub>t</sub>, s<sub>t+1</sub>)* where *p(M<sub>k</sub>)* denotes the probability of selecting the *k<sup>th</sup> model primitive*. This is hard to compute as the system does not have access to *s<sub>t+1</sub>*  and *a<sub>t</sub>* at time *t* before it has choosen the sub-policy.

* Properly marginalizing *s<sub>t+1</sub>* and *a<sub>t</sub>* would require expensive MC sampling. Hence an approximation is used and the gating controller is modeled as a categorical distribution - to produce *p(M<sub>k</sub> \| s<sub>t</sub>)*. This is trained via a conditional cross entropy loss where the ground truth distribution is obtained from transitions sampled in a rollout. 

* The paper notes that technique is biased but reports that it still works for the downstream tasks.

* The gating controller composes the sub-policies as a mixture of Gaussians.

* For learning, PPO algorithm is used with each *model primitives* gradient weighted by the probability from the gating controller.

## Lifelong Learning

* Different tasks could share common subtasks but may require a different composition of subtasks. Hence, the learned sub-policies are transferred across tasks but not the gating controller or the baseline estimator (from PPO).

## Experiments

* Domains:

    * Mujoco ant navigating different mazes.

    * Stacker arm picking up and placing different boxes.

* Implementation Details:

    * Gaussian subpolicies

    * PPO as the baseline

    * Model primitives are hand-crafted using the true next state provided by the environment simulator.

* Single Task

    * Only maze task is considered with the start position (of the ant) and the goal position is fixed.

    * Observation includes distance from the goal.

    * Forcing the agent to decompose the problem, when a more direct solution may be available, causes the sample complexity to increase on one task.

* Lifelong Learning

    * Maze

        * 10 random Mujoco ant mazes used as the task distribution.

        * MPHRL takes almost twice the number of steps (as compared to PPO baseline) to solve the first task but this cost gets amortized over the distribution and the model takes half the number of steps as compared to the baseline (summed over the 10 tasks).

    * Pick and Place

        * 8 Pick and Place tasks are created with max 3 goal locations.

        * Observation includes the position of the goal.

* Ablations

    * Overlapping *model primitives* can degrade the performance (to some extent). Similarly, the performance suffers when redundant primitives are introduced indicating that the gating mechanism is not very robust.

    * Sub-policies could quickly adapt to the previous tasks (on which they were trained initially) despite being finetuned on subsequent tasks.

    * The order of tasks (in the 10-Mazz task) does not degrage the performance.

    * Transfering the gating controller leads to negative transfer.

* Notes

    * I think the biggest strength of the work is that accurate dynamics model are not needed (which are hard to train anyways!) through the experimental results are not conclusive given the limited number of domains on which the approach is tested.