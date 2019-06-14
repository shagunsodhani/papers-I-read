---
layout: post
title: Extrapolating Beyond Suboptimal Demonstrations via Inverse Reinforcement Learning from Observations
comments: True
excerpt: 
tags: ['2019', 'ICML 2019', 'Inverse Reinforcement Learning', 'Reinforcement Learning', AI, ICML, IRL, RL]

---
## Introduction

* The paper proposes a new inverse RL (IRL) algorithm, called as Trajectory-ranked Reward EXtrapolation (T-REX) that learns a reward function from a collection of ranked trajectories.

* Standard IRL approaches aim to learn a reward function that "justifies" the demonstration policy and hence those approaches cannot outperform the demonstration policy.

* In contrast, T-REX aims to learn a reward function that "explains" the ranking over demonstrations and can learn a policy that outperforms the demonstration policy.

* [Link to the paper](https://arxiv.org/abs/1904.06387)

## Approach

* The input is a sequence of trajectories *T<sub>1</sub>, ... T<sub>m</sub>* which are ranked in the order of preference. That is, given any pair of trajectories, we know which of the two trajectories is better.

* The setup is to learn from observations where the learning agent does not have access to the true reward function or the action taken by the demonstration policy.

* Reward Inference
    
    * A parameterized reward function *r<sub>&theta;</sub>* is trained with the ranking information using a binary classification loss function which aims to predict which of the two given trajectory would be ranked higher.

    * Given a trajectory, the reward function predicts the reward for each state. The sum of rewards (corresponding to the two trajectories) is used used to predict the preferred trajectory.

    * T-REX uses partial trajectories instead of full trajectories as a data augmentation strategy.

* Policy Optimization

    * Once a reward function has been learned, standard RL approaches can be used to train a new policy.

## Results

* Environments: Mujoco (Half Cheetah, Ant, Hooper), Atari

* Demonstrations generated using PPO (checkpointed at different stages of training).

* Ensemble of networks used to learn the reward functions.

* The proposed approach outperforms the baselines [Behaviour Cloning from Observations](https://arxiv.org/abs/1805.01954) and [Generative Adversarial Imitation Learning](https://arxiv.org/abs/1606.03476).

* In terms of reward extrapolation, T-REX can predict the reward for trajectories which are better than the demonstration trajectories.

* Some ablation studies considered the effect of adding noise (random swapping the preference between trajectories) and found that the model is somewhat robust to noise up to an extent.