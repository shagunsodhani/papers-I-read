---
layout: post
title: Observational Overfitting in Reinforcement Learning
comments: True
excerpt: 
tags: ['2019', 'ICLR 2020', 'Deep Reinforcement Learning', 'Evaluating Generalization', 'Markov Decision Process', 'Reinforcement Learning', AI, DRL, Evaluation, Generalization, ICLR, MDP, RL]

---


## Introduction

* The paper studies *observational overfitting*: The phenomenon where an agent overfits to different observation spaces even though the underlying MDP remains fixed.

* Unlike other works, the "background information" (in the pixel space) is correlated with the progress of the agent (and is not just noise).

* [Link to the paper](https://arxiv.org/abs/1912.02975)

## Setup

* Base MDP $M = (S, A, R, T)$ where $S$ is the state space, $A$ is the action space, $R$ is the reward function, and $T$ is the transition dynamics. 

* $M$ is parameterized using $\theta$. In practice, it means introducing an observation function $\phi_{\theta}$ ie $M_{\theta} = (M, \phi_{\theta})$.

* A distribution over $\theta$ defines a distribution over the MDPs.

* The learning agent has access to the pixel space observations and not the state space observations.

* Generalization gap is defined as $J_{\theta}(\pi) - J_{\theta^{train}}(\pi)$ where $\pi$ is the learning agent, $\theta$ is the distribution over all the observation functions, $\theta^{train}$ is the distribution over the observation functions corresponding to the training environments. $J_{\theta}(\pi)$ is the average reward that the agent obtains over environments sampled from $M_{\theta}$.

* $\phi_{\theta}$ considers two featurs - generalizable (invariant across $\theta$) and non-generalizable (depends on $\theta$) ie $\phi_{\theta}(s) = concat(f(s), g_{\theta}(s))$ where $f$ is the invariant function and $g$ is the non-generalizable function.

* The problem is set up such that "explicit regularization" can easily solve it. The focus is on understanding the effect of "implicit regularization".

## Experiments

### Overparameterized LQR

* LQR is used as a proxy for deep RL architectures given its advantages like enabling exact gradient descent.

* The functions are parameterized as follows:
    
    * $f(s) = W_c(s)$

    * $g_{\theta}(s) = W_{\theta}(s)$

* Observation at time $t$ , $o_t$, is given as $[W_c W_{\theta}]^{-1} s_t$.

* Action at time $t$ is given as $a_t = K o_{t}$ where $K$ is the policy matrix.

* Dimensionality:
    
    * state $s$: $d_{state}$ 100
    * $f(s)$: $d_{state}$ 100
    * $g_{\theta}(s)$: $d_{noise}$ 100
    * observation $o$: $d_{state}$ + $d_{noise}$ 1100

* In case of training on just one environment, multiple solutions exist, and overfitting happens.

* Increasing $d_{noise}$ increases the generalization gap.

* Overparameterizing the network decreases the generalization gap and also reduces the norm of the policy.

### Projected Gym Environments

* The base MDP is the Gym Environment.

* $M_{\theta}$ is generated as before.

* Increasing both width and depth for basic MLPs improves generalization.

* Generalization also depends on the choice of activation function, residual layers, etc.

### Deconvolutional Projections

* In the Gym environment, the actual state is projected to a larger vector and reshaped into an 84x84 tensor (image).

* The image from $f$ is concatenated with the image from $g$. This setup is referred to as the Gym-Deconv.

* The relative order of performance between NatureCNN, IMPALA, and IMPALA-Large (on both CoinRun and Gym-Deconv) is the same as the order of the number of parameters they contain.

* In an ablation, the policy is given access to only $g_{\theta}(s)$, which makes it impossible for the model to generalize. In this test of memorization capacity, implicit regularization seems to reduce the memorization effect.

### Overparameterization in CoinRun

* The pixel space observation in CoinRun is downsized from 64x64 to 32x32 and flattened into a vector.

* In CoinRun, the dynamics change per level, and the noisy "irrelevant" features change location across the 1D input, making this setup more challenging than the previous ones.

* Overparameterization improves generalization in this scenario as well.
