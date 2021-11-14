---
layout: post
title: Synthesized Policies for Transfer and Adaptation across Tasks and Environments
comments: True
excerpt: 
tags: ['2018', 'Transfer Learning', 'Inverse Reinforcement Learning', 'Reinforcement Learning', 'NeurIPS 2018', AI, Compositionality, Generalizatio, IRL, NeurIPS, RL]
---

## Introduction

* The paper studies transfer learning in RL, focusing on simultaneous transfer across both tasks and environments.

* The key idea is to learn task and environment embeddings and compose them using a meta-rule, and the proposed approach is called SYNPO (Synthesized Policies).

* [Link to the paper](https://arxiv.org/abs/1904.03276)

## Setup

* Three settings considered:

    * *S1*: Transfer to a new (environment, task) pair when the agent has been trained on the environment and the task before (but not simultaneously). 
    
    * *S2*: Transfer to a new (environment, task) pair where either the environment or the task is not seen previously. 

    * *S3*: Transfer to a new (environment, task) pair where neither the environment nor the task is seen previously. 

* In the second and third settings, the agent is allowed to collect some data in the new environment or task.

* The (environment, task) combinations that the agent has seen during training are referred to as *seen* combinations, while the remaining combinations are referred to as the *unseen* combinations.

* The key idea is to:
    
    * learn embeddings of environments and tasks
    
    * use these embeddings to compose a policy (parameterized as the linear combination of the policy basis).

* A disentanglement objective is used to decouple the task and environment embedding. 

### Policy Composition

* Given an (environment, task) pair $z = (\epsilon, \tau)$, the policy is given as $\pi_z(a\|s) \propto exp(\psi_s^TU(e_{\epsilon}, e_{\tau})\phi_{a} + b_{\pi} )) $.

* Here $b_{\pi}$ is a scalar bias, $\psi_{s}$ and $\phi_{a}$ are state and action representations, $U$ is parameterized as the linear comination of $K$ basis matrices $\Theta_k$

* $U(e_{\epsilon}, e_{\tau}) = \sum_{k=1}^{K}\alpha_k(e_{\epsilon}, e_{\tau})\Theta_k$.

* The basis matrices (denoted by $\Theta_k$) are shared across tasks while the coefficients ($\alpha_k$) are specific to the (environment, task) pair.

* During training, the agent also predicts rewards using the same set of basis but different coefficients.

### Disentangling environment and task embeddings

* Given an (environment, task) pair, the agent is trained to decode the environment (and task) given the agent's trajectory. 

* The sequence of state-action pairs (in the trajectory) is mapped to a sequence of state-action representations, given by $\psi_s^T\Theta_k\phi_{a}$

## Experiment Setup

* The agent is trained (and evaluated) on imitation learning (mostly) and reinforcement learning setup.

### Environments

* GRIDWORLD

    * Twenty $16 \times 16$ gird-aligned mazes that are similar in appearance but differ in topology.

    * The task is to collect colored blocks in a given order. In each task, the starting position of the agent and the position of the blocks is randomized.
    
    * Each environment has 20 tasks, leading to a total of 400 (environment, task) combinations.

* [THOR](https://arxiv.org/abs/1712.05474)

    * This is a 3D simulator where the agent is placed in indoor photo-realistic scenes.

    * The task is the search for objects and perform actions like "put cabbage on the fridge."

    * The setup uses 19 scenes (environments), with each environment comprising of 21 tasks.

### Baselines

* MLPs that concatenate state, environment embeddings, and task embedding.

* [Successor feature model](https://arxiv.org/abs/1606.05312)

* [Module Network](https://arxiv.org/abs/1609.07088)

* Multi-task Learning where the distinction between the environments is ignored.

## Results

* GRIDWORLD

    * In the first setting (*S1*)
        
        * SYNPO outperforms all the baselines.

        * As the agent is trained on more (environment, task) combinations, its performance on the unseen combinations improves. This trend saturates when the *seem/total* ratio reaches about 0.4 (i.e., training on 40% of all the combinations).

        * Task disentanglement is more important than environment disentanglement.

    * In the second and third setting (*S2* and *S3*)

        * The agent uses one demonstration from each test pair to finetune the embeddings.

        * *S2* is an easier setting than *S3*.

        * Transfer learning across tasks is easier than transfer learning across environments.

*  THOR

    * SYNPO outperforms all the baselines on both seen and unseen combinations.

  