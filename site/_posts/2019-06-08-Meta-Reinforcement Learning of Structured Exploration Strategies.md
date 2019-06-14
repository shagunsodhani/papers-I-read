---
layout: post
title: Meta-Reinforcement Learning of Structured Exploration Strategies
comments: True
excerpt: 
tags: ['2018', 'Meta Learning', 'Meta Reinforcement Learning', 'NeurIPS 2018', 'Reinforcement Learning', 'Structured Exploration', AI, Exploration, NeurIPS, RL]

---
## Introduction

* The paper looks at the problem of learning structured exploration policies for training RL agents.

* Link to the [paper](https://arxiv.org/abs/1802.07245)

## Structured Exploration

* Consider a stochastic, parameterized policy &pi;<sub>&theta;</sub>(a\|s) where &theta; represents the *policy-parameters*.

* To encourage exploration, noise can be added to the policy at each time step t. But the noise added in such a manner does not have any notion of temporal coherence.

* Another issue is that if the policy is represented by a simple distribution (say parameterized unimodal Gaussian), it can not model complex time-correlated stochastic processes.

* The paper proposes to condition the policy on per-episode random variables (z) which are sampled from a learned latent distribution.

* Consider a distibution over the tasks p(T). At the start of any episode of the i<sup>th</sup> task, a latent variable z<sub>i</sub> is sampled from the distribution *N(&mu;<sub>i</sub>, &sigma;<sub>i</sub>)* where &mu;<sub>i</sub> and &sigma;<sub>i</sub> are the learned parameters of the distribution and are referred to as the *variation parameters*.

* Once sampled, the same *z<sub>i</sub>* is used to condition the policy for as long as the current episode lasts and the action is sampled from then distribution &pi;<sub>&theta;</sub>(a\|s, z<sub>i</sub>).

* The intuition is that the latent variable z<sub>i</sub> would encode the notion of a task or goal that does not change arbitrarily during the episode.

## Model Agnostic Exploration with Structured Noise

* The paper focuses on the setting where the structured exploration policies are to be learned while leveraging the learning from prior tasks. 

* A meta-learning approach, called as model agnostic exploration with structured noise (MAESN) is proposed to learn a good initialization of the *policy-parameters* and to learn a latent space (for sampling the z from) that can inject structured stochasticity in the policy.

* General meta-RL approaches have two limitations when it comes to "learning to explore":

    * Casting meta-RL problems as RL problems lead to policies that do not exhibit sufficient variability to explore effectively.
    * Many current approaches try to meta-learn the entire learning algorithm which limits the asymptotic performance of the model.

* Idea behind MAESN is to meta-train *policy-parameters* so that they learn to use the task-specific *latent variables* for exploration and can quickly adapt to a new task.

* An important detail is that the parameters are optimized to maximize the expected rewards after one step of gradient update to ensure that the policy uses the latent variables for exploration.

* For every iteration of meta-training, an "inner" gradient update is performed on the variational parameters and the *post-inner-update* parameters are used to perform the meta-update.

* The authors report that performing the "inner" gradient update on the *policy-parameters* does not help the overall learning objective and that the step size for each parameter had to be meta-learned.

* The variation parameters have the usual KL divergence loss which encourages them to be close to the prior distribution (unit Gaussian in this case).

* After training, the *variational parameters* for each task are quite close to the prior probably because the training objective optimizes for the expected reward after one step of gradient descent on the *variational parameters*. 

* Another implementation detail is that reward shaping is used to ensure that the policy gets useful signal during meta-training. To be fair to the baselines, reward shaping is used while training baselines as well. Moreover, the policies trained with reward shaping generalizes to sparse reward setup as well (during meta-test time).

## Experiments

* Three tasks distributions: Robotic Manipulation, Wheeled Locomotion, and Legged Locomotion. Each task distribution has 100 meta-training tasks.

* In the Manipulation task distribution, the learner has to push different blocks from different positions to different goal positions. In the Locomotion task distributions, the different tasks correspond to the different goal positions.

* The experiments show that the proposed approach can adapt to new tasks quickly and the learn coherent exploration strategy.

• In some cases, learning from scratch also provides a strong asymptotic performance although learning from scratch takes much longer.