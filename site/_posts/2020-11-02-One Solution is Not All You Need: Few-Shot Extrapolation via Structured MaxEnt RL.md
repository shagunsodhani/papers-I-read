---
layout: post
title: One Solution is Not All You Need - Few-Shot Extrapolation via Structured MaxEnt RL
comments: True
excerpt: 
tags: ['2020', 'Deep Reinforcement Learning', 'Latent Variable', 'NeurIPS 2020', 'Reinforcement Learning', AI, DRL, Generalization, NeurIPS, RL]


---

## Introduction

* Key idea: Practicing and remembering diverse solutions to a task can lead to robustness to that task's variations.

* The paper proposes a framework to implement this idea - train multiple policies such that they are *collectively* robust to a new distribution over environments while using a single training environment.

* [Link to the paper](https://arxiv.org/abs/2010.14484)

## Setup

* During training, the agent has access to only one MDP.

* During the evaluation, the agent encounters a new MDP which has the same state and action space but may have a different reward and transition function.

* The agent is allowed some interactions (say *k*) with the test MDP and is then evaluated on the test MDP. The setup is referred to as *few-shot robustness*.

## Structured Maximum Entropy Reinforcement Learning (SMERL)

* Represent a set of policies using a latent variable policy (i.e., a policy conditioned on a latent variable *z*).

* This has two benefits: (i) Multiple policies can be represented by the same object, and (ii) diverse behaviors can be learned by encouraging the trajectories, corresponding to different *z* to be different, while being able to solve the task.

* A diversity-inducing objective is used to encourage the agent to learn different trajectories for different *z*. 

* Specifically, the mutual information between *p(Z)* and marginal trajectory distribution for the latent variable policy is maximized, subject to the constraint that each policy achieves close to optimal returns in the train MDP.

* The mutual information between *p(Z)* and marginal trajectory distribution for the latent variable policy is lower bounded by the sum of mutual information terms over individual states (appearing in the trajectory).

*  An unsupervised reward function is defined using the mutual information between states and latent variables. 

* $$r(s, a) = log(q_{\phi})(z\|s) - log(p(z))$$ where $$q_{\phi}$$ is a learned discriminator.

* This unsupervised reward is optimized for only when the policy achieves close to an optimal return, i.e., the environment return is close to the optimal return. Otherwise, the agent optimizes only for the environment return.

### Implementation

* SMERL is implemented using SAC with a latent variable maximum entropy policy.

* The set of latent variables is a fixed discrete set $$Z$$ and $$p(z)$$ is set to be a uniform distribution over this set.

* At the start of an episode, a $$z$$ is sampled and used throughout the episode.

* Discriminator $$q_{\phi}(z\|s)$$ is trained to infer $$z$$ from the visited states.

* A baseline SAC agent is trained beforehand to evaluate if the current training policy achieves close to optimal environment return.

* During the evaluation, the policy corresponding to each latent variable is executed in the test MDP, and the policy with the maximum return is returned.

## Theoretical Analysis

* Given an MDP $$M$$ and $$\epsilon>0$$, the MDP robustness set is defined as the set of all MDPs $$M'$$ where the optimal policy of $$M'$$ produces the same trajectory distribution in $$M'$$ as $$M$$. Moreover, on the training MDP $$M$$, the optimal policies (corresponding to $$M$$ and $$M'$$) obtain similar returns.

* The paper shows that SMERL generalizes to MDPs belong to the robustness set. 

* It also provides a simplified view of the optimization objective and shows how it naturally leads to a trajectory-centric mutual information objective.

## Experiments

* Environments

    * 2D navigation environments with point mass.
    
    * Mujoco Environments: HalfCheetah-Goal, Walker2d-Velocity, Hopper-Velocity.

* On the 2D navigation environment, the paper shows that SMERL learns to use different trajectories to reach the goal.

* On the Mujoco setup, the evaluation shows that SMERL generally outperforms the best-performing baseline or is close to the best-performing baseline on different tasks.

* Generally, higher train performance does not correlate with higher test performance, and there is no single policy that performs the best across all the tasks. Thus, it should be beneficial to learn multiple diverse policies that can be selected from during testing.