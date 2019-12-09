---
layout: post
title: Contrastive Learning of Structured World Models
comments: True
excerpt: 
tags: ['2019', 'Graph Neural Network', 'Object-Oriented Learning', 'Relational Learning', AI, Graph, GNN]

---

## Introduction

* The paper introduces Contrastively-trained Structured World Models (C-SWMs).

* These models use a contrastive approach for learning representations in environments with compositional structure.

* [Link to the paper](https://arxiv.org/abs/1911.12247)

* [Link to the code](https://github.com/tkipf/c-swm).

## Approach

* The training data is in the form of an experience buffer $$B = \{(s_t, a_t, s_{t+1})\}_{t=1}^T$$ of state transition tuples.

* The goal is to learn:
    
    * an encoder $$E$$ that maps the observed states $s_t$ (pixel state observations) to latent state $z_t$.

    * a transition model $$T$$ that predicts the dynamics in the hidden state.

* The model defines the enegry of a tuple $$(s_t, a_t, s_{t+1})$$ as $$H = d(z_t + T(z_t, a_t), z_{t+1})$$.

* The model has an inductive bias for modeling the effect of action as translation in the abstract state space.

* An extra hinge-loss term is added: $$max(0, \gamma - d(z^{~}_{t}, z_{t+1}))$$ where $$z^{~}_{t} = E(s^{~}_{t})$$ is a corrputed latent state corresponding to a randomly sampled state $$s^{~}_{t}$$.

## Object-Oriented State Factorization

* The goal is to learn object-oriented representations where each state embedding is structured as a set of objects.

* Assuming the number of object slots to be $$K$$, the latent space, and the action space can be factored into $$K$$ independent latent spaces ($$Z_1 \times ... \times Z_K$$) and action spaces ($$A_1 \times ... \times A_k$$) respectively.

* There are *K* CNN-based object extractors and an MLP-based object encoder.

* The actions are represented as one-hot vectors.

* A fully connected graph is induced over *K* objects (representations) and the transition function is modeled as a Graph Neural Network (GNN) over this graph.

* The transition function produces the change in the latent state representation of each object.

* The factorization can be taken into account in the loss function by summing over the loss corresponding to each object.

## Environments

* Grid World Environments - 2D shapes, 3D blocks

* Atari games - Pong and Space Invaders

* 3-body physics simulation

## Setup

* Random policy is used to collect the training data.

* Evaluation is performed in the latent space (no reconstruction in the pixel space) using ranking metrics. The observations (to compare against) are randomly sampled from the buffer.

* Baselines - auto-encoder based World Models and [Physics as Inverse Graphics model](https://arxiv.org/abs/1905.11169).

## Results

* In the grid-world environments, C-SWM models the latent dynamics almost perfectly.

* Removing either the state factorization or the GNN transition model hurts the performance.

* C-SWM performs well on Atari as well but the results tend to have high variance.

* The optimal values of $K$ should be obtained by hyperparameter tuning.

* For the 3-body physics tasks, both the baselines and proposed models work quite well.

* Interestingly, the paper has a section on limitations:
    
    * The object extractor module can not disambiguate between multiple instances of the same object (in a scene).

    * The current formulation of C-SWM can only be used with deterministic environments.