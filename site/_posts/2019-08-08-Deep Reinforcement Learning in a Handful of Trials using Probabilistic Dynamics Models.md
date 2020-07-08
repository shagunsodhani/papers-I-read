---
layout: post
title: Deep Reinforcement Learning in a Handful of Trials using Probabilistic Dynamics Models
comments: True
excerpt: 
tags: ['2018', 'Deep Reinforcement Learning', 'Model-Based', 'Neurips 2018', 'Reinforcement Learning',  AI, DRL, Neurips, RL]

---

## Introduction

* The paper proposes a new algorithm called as Probabilistic Ensemble with Trajectory sampling (PETS) that combines uncertainty aware deep learning models (ensemble of deep learning models that encode uncertainty) with sampling-based uncertainty propagation.

* PETS improves over other probabilistic MBRL approaches by isolating epistemic uncertainty (due to limited training data) and aleatoric uncertainty (inherent in the system). 

* [Link to the paper]()

## Uncertainty-Aware Neural Network Dynamics Model

* Aleatoric uncertainty can be accounted for by learning a parameterized distribution (probabilistic neural network) trained with negative log-likelihood.

* Epistemic uncertainty can be accounted for by either having an infinite amount of data or by using ensembles.

* The paper uses a neural network to predict the mean and standard deviation of a gaussian distribution which defines the predictive model. This setup is referred to as the "probabilistic" model and denoted by **P**.

* The alternate setup of the deterministic model is where a neural network is used to make a point prediction (and is denoted by **D**).

* Ensemble of probabilistic models is denoted as **PE** while that of deterministic models is denoted as **DE**.

## Planning and Control with learned Dynamics

* Model Predictive Control (MPC) is used for planning.

* Given a start state and an action sequence, the probabilistic dynamics model induces a distribution over the trajectories.

* The first action, among the sequence of optimized actions, is executed.

* Instead of random shooting, [Cross Entropy Method (CEM)](https://www.sciencedirect.com/science/article/pii/B9780444538598000035) is used.

## Trajectory Sampling

* Let us say there are B bootstrap models in the ensemble. Given the current state, P particles are created and each particle is propagated using one of the bootstrap models. Two variants are considered:

    * TS1 - At each timestep, each particle samples a bootstrap. In this case, particle separation can not be attributed to ti the compounding effects of the bootstraps.

    * TS$\infty$ - The bootstrapped model (per particle) is sampled just once and is not changed after that. This setup separates aleatoric and epistemic uncertainty. Aleatoric state variance is the average variance of the particles of the same bootstrap while epistemic state variance is the variance of the average of particles of same bootstrap indexes.

## Result

* The proposed approach reaches the asymptotic performance of state-of-the-art model-free algorithms in much fewer samples.

* The general performance trend is probabilistic emsemble > probabilisitc model > deterministc ensemble > determinisitc model./.

* Initial experiments for learning policy by propagating gradients through the ensemble of models did not work and has been left as future work.
