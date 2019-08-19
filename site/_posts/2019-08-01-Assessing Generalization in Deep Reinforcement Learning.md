---
layout: post
title: Assessing Generalization in Deep Reinforcement Learning
comments: True
excerpt: 
tags: ['2018', 'Deep Reinforcement Learning', 'Evaluating Generalization', 'Reinforcement Learning',  AI, DRL, Evaluation, Generalization, RL]

---

* The paper presents a benchmark and experimental protocol (environments, metrics, baselines, training/testing setup) to evaluate RL algorithms for generalization.

* Several RL algorithms are evaluated and the key takeaway is that the "vanilla" RL algorithms can generalize better than the RL algorithms that are specifically designed to generalize, given enough diversity in the distribution of the training environments.

* [Link to the paper](https://arxiv.org/abs/1810.12282)

* The focus is on evaluating generalization to environmental changes that affect the system dynamics (and not the goal or rewards).

* Two generalization regimes are considered:
    
    * Interpolation - parameters of the test environment are similar to the parameters of the training environment.

    * Extrapolation - parameters of the test environment are different from the parameters of the training environment.

* Following algorithms are considered as part of the benchmark:

    * "Vanilla" RL algorithms - A2C, PPO

    * RL algorithms that are designed to generalize:

        * EPOpt - Learn a (robust) policy that maximizes the expected reward over the most difficult distribution of environments (ones with the worst expected reward).

        * RL<sup>2</sup> - Learn an (adaptive) policy that can adapt to the current environment/task by considering the trajectory and not just the state transition sequence.

    * These specially designed RL algorithms can be optimized using either A2C or PPO leading to combinations like EPOpt-A2C or EPOpt-PPO etc.

    * The models are either composed of feedforward networks completely or feedforward + recurrent networks.

* Environments

    * CartPole, MountainCar, Acrobot, and Pendulum from OpenAI Gym.

    * HalfCheetah and Hopper from OpenAI Roboschool.

    * Three versions of each environment are considered:

        * Deterministic: Environment parameters are fixed. This case corresponds to the standard environment setup in classical RL.

        * Random: Environment parameters are sampled randomly. This case corresponds to sampling from a distribution of environments.

        * Extreme: Environment parameters are sampled from their extreme values. This case corresponds to the edge-case environments which would not be encountered during training generally.

* Performance Metrics
    
    * Average total reward per episode.

    * Success percentage: Percentage of episodes where a certain goal (or reward) is obtained.

* Evaluation Metrics/Setups

    * Default: success percentage when training and evaluating the deterministic version of the environment.  

    * Interpolation: success percentage when training and evaluating on the random version of the environment.

    * Extrapolation: the geometric mean of the success percentage of following three versions:

        * Train on deterministic and evaluate on the random version.

        * Train on deterministic and evaluate on extreme version.

        * Train on random and evaluate on the extreme version.

* Observations

    * Extrapolation is harder than interpolation.

    * Increasing the diversity in the training environments improves the interpolation generalization of vanilla RL methods.

    * EPOpt improves generalization only for continuous control environments and only with PPO.

    * RL<sup>2</sup> is difficult to train on the environments considered and did not provide a clear advantage in terms of generalization.

    * EPOpt-PPO outperforms PPO on only 3 environments and EPOpt-A2C does not 

