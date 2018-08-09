---
layout: post
title: Imagination-Augmented Agents for Deep Reinforcement Learning
comments: True
excerpt: 
tags: ['2017', 'NIPS 2017', AI, Model-Based, Model-Free, NIPS, RL]
---

* The paper presents I2A (Imagination Augmented Agent) that combines the model-based and model-free approaches leading to data efficiency and robustness even with imperfect models.

* I2A agent uses the predictions from a learned environment model as an additional context in deep policy networks. This leads to improved data efficiency and robustness to imperfect models. 

* [Link to the paper](https://arxiv.org/abs/1707.06203)

* I2A agent has two main modules - Imagination module and the Policy module.

* **Imagination Module** 

    * **Environment Model**
      * This is a recurrent model, trained in an unsupervised manner using the agent trajectories. It can be used to predict the future state given the current state and action.
      * The environment model can be rolled out multiple times to obtain a simulated trajectory or an "imagined" trajectory.
      * During each rollout, the actions are chosen using a rollout policy &pi;<sub>r</sub>.

    * **Rollout Encoder**
        * A rollout encoder *E* (LSTM) is used to process the entire imagined rollout.
    * The imagination module is used to generate *n* trajectories. Each trajectory is a sequence of outputs of the environment model.
    * These *n* trajectories are concatenated into a single "imagination" vector.
    * The training data for the environment model is generated from trajectories of a partially trained model-free agent.
    * Pretraining the environment model (instead of joint training with policy) leads to faster runtime.
    
    
* **Policy Module**
    
    * This module uses the output of both model-based path and model-free path as its input. It generates the policy vector and value function.
      

* **Rollout Strategy**
    * One rollout is performed for each possible action in the environment ie, the first action in the i<sup>th</sup> rollout is the i<sup>th</sup> action in the action set.
    * Subsequent actions are generated using a shared rollout policy &pi;<sub>'</sub>
    * An effective strategy was to create a small model-free network &pi;<sub>'</sub>(o<sub>t</sub>) and then add a KL loss component that encourages &pi;<sub>'</sub>(o<sub>t</sub>)to be similar to the imagination augmented policy &pi;(o<sub>t</sub>).

* **Baselines**
    * Model-free agent
    * Copy-model agent - same as I2A but the environment model is replaced by a "copy" model that just returns the input observations.
      
* **Environments**
    * Sokoban
        * Task is to push a number of boxes onto given target locations.
        * I2A outperforms the baselines and gains in performance as the number of unrolling steps increases (though at a diminishing rate).
        * In case of poor environment models, the agent seems to be able to ignore the later part of the rollout when the error starts to accumulate.
        * Monte Carlo search algorithm (without an explicit rollout encoder) performed poorly as compared to the model using rollout encoder.
        * Predicting the reward along with value function and action seems to speed up training.
        * If a near-perfect model is available, I2A agent's performance can be improved by performing Monte Carlo search with the trained I2A agent for the rollout policy. The agent plays entire episodes in simulation and tries to find a successful action sequence within 10 retries.
   
   * **MiniPacman**
        * I2A agent is evaluated to see if a single model can be used to solve multiple tasks.
        * A new environment is designed to define multiple tasks in an environment with shared state transitions.
        * Each task is specified by a 5-dimensional reward vector that associates a reward with moving, eating food, eating a pill, eating a ghost and being eaten by a ghost.
        * A single environment model is trained to predict both observations (frames) and events (eg "eating a pill"). This way, the environment model is shared across all tasks. 
        * Baseline agents and I2As are trained on each task separately. I2A architecture outperforms the standard agent in all tasks and the copy-model
baseline in all but one task. 
        * The improvement in performance is higher for tasks where rewards are sparse and where the anticipation
of ghost dynamics is especially important indicating that the I2A agent can use the environment model to explore the environment more effectively.