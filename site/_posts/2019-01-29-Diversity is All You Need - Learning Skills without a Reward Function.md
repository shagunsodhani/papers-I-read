---
layout: post
title: Diversity is All You Need - Learning Skills without a Reward Function
comments: True
excerpt: 
tags: ['2019', 'ICLR 2019', 'Information Theory', 'Reinforcement Learning', Entropy, ICLR, RL, Unsupervised]
---

## Introduction

* The paper proposes an approach to learn useful skills without a reward function by maximizing an information theoretic objective by using a maximum entropy policy.

* Skills are defined as latent-conditioned policies that alter the state of the environment in a consistent way.

* [Link to the paper](https://arxiv.org/abs/1802.06070)

* [Link to the code](https://github.com/ben-eysenbach/sac)

## Setup

* Unsupervised "exploration" stage followed by supervised stage.

## Desirable Qualities of Skills

* Skills should dictate the states that the agent visits. Different skills should visit different states to be distinguishable.

* States (not actions) should be used to distinguish between skills as not all actions change the state (for the outside observer).

* Skills are encouraged to be diverse and "exploratory" by learning skills that act randomly (have high entropy).

## Loss Formulation

* (S, A) - state and action

* z ~ p(z) - latent variable to condition the policy.

* Skill - policy conditioned on a fixed z.

* Objective is to maximize the mutual information between skill and state (MI(A; Z)) ie skill should control which state is visited or the skill should be inferrable from the state visited.

* Simultaneously minimize the mutual information between skills and actions given the state to ensure that the state (and not the action) is used to distinguish the skills.

* Maximize the entropy of the mixture of policies (p(z) and all the skills).

## Implementation

* Policy &pi;(a \| s, z)

* Task reward replaced by the pseduoreward logq<sub>&phi;</sub>(z \| s) - log(p(z)).

* During unsupervised training, z is sampled at the start of the episode and then not changed during the episode.

* Learning agent gets rewards for visiting the states that are easy to discriminate while the discriminator updated to correctly predict z from the states visited.

## Observations

### Analysis of Learned Skills

* The agent learns a diverse set of primitive behaviors for all tasks ranging from 2 DoF to 111 DoF.

* for inverted pendulum and mountain car, the skills become increasingly diverse throughout training.

* Use of uniform prior, in place of a learned prior, for p(z) allows for discovery of more diverse skills.

* The proposed approach can be used as a pretraining technique where the best-performing primitives (from unsupervised training) can be finetuned with the task-specific rewards.

* The discovered skills can be used for hierarchical RL by learning a meta-policy(which chooses the skill to execute for k steps).

* Modifying the discriminator in the proposed formulation can be used to bias DIAYN towards discovering a particular type of policies. This provides a mechanism for incorporating "supervision" in the learning setup.

* The "discovered" primitives can also be used for imitation learning. 
