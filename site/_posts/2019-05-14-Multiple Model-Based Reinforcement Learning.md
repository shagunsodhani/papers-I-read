---
layout: post
title: Multiple Model-Based Reinforcement Learning
comments: True
excerpt: 
tags: ['2002', 'Model-Based', 'Neural Computation', 'Neural Computation 2002', 'Reinforcement Learning', AI, RL]
---


* The paper presents some general ideas and mechanisms for multiple model-based RL. Even though the task and model architecture may not be very relevant now, I find the general idea and the mechanisms to be quite useful. As such, I am focusing only on high-level ideas and not the implementation details themselves.

* The main idea behind Multiple Model-based RL (MMRL) is to decompose complex tasks into multiple domains in space and time so that the environment dynamics within each domain is predictable.

* [Link to the paper](https://www.mitpressjournals.org/doi/abs/10.1162/089976602753712972)

* MMRL proposes an RL architecture composes of multiple modules, each with its own state prediction model and RL controller.

* The prediction error from each of the state prediction model defines the "responsibility signal" for each module.

* This responsibility signal is used to: 

    * Weigh the state prediction output ie the predicted state is the weighted sum of individual state predictions (weighted by the responsibility signal).

    * Weigh the parameter update of the environment models as well as the RL controllers.

    * Weighing the action output - ie predicted action is a weighted sum of individual actions.

* The framework is amenable for incorporating prior knowledge about which module should be selected.

* In the modular decomposition of a task, the modules should not change too frequently and some kind of spatial and temporal continuity is also desired.

* Temporal continuity can be accounted for by using the previous responsibility signal as input during the current timestep.

* Spatial continuity can b ensured by considering a spatial prior like the Gaussian spatial prior.

* Though model-free methods could be used for learning the RL controllers, model-based methods could be more relevant given that the modules are learning state-prediction models as well.

* Exploration can be ensured by using a stochastic version of greedy action selection.

* One failure mode for such modular architectures is when a single module tries to perform well across all the tasks. The modules themselves should be relatively simplistic (eg linear models) which can learn quickly and generalize well.

* Non-stationary hunting task in a grid world and non-linear, non-stationary control task of swinging up a pendulum provides the proof of concept for the proposed methods.
