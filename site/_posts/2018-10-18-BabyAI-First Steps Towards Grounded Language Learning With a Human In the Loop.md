---
layout: post
title: BabyAI - First Steps Towards Grounded Language Learning With a Human In the Loop
comments: True
excerpt: 
tags: ['2018', 'Curriculum Learning', 'Grounded Language Learning', 'ICLR 2018', 'Interactive Teaching', 'Natural Language Processing', 'Reinforcement Learning', AI, Environment, ICLR, NLP, RL]
---

## Introduction

* BabyAI is a research platform to investigate and support the feasibility of including humans in the loop for grounded language learning.

* The setup is a series of levels (of increasing difficulty) to train the agent to acquire a synthetic language (Baby Language) which is a proper subset of English language.

* [Link to the paper](https://arxiv.org/abs/1810.08272)

## Motivation

* BabyAI platform provides support for curriculum learning and interactive learning as part of its human-in-the-loop training setup. 

* Curriculum learning is incorporated by having a curriculum of levels of increasing difficulty.

* Interactive learning is supported by including a heuristic expert which can provide new demonstrations on the fly to the learning agent.

* The heuristic expert can be thought of as the human-in-the-loop which can guide the agent through the learning process.

* One downside of human-in-the-loop is the poor sample complexity of the learning agent. The heuristic agent can be used to estimate the sample  efficiency.

## Contribution

* BabyAI research platform for grounded language learning with a simulated human-in-the-loop.

* Baseline results for performance and sample efficiency for the different tasks.

## BabyAI Platform

### Environment

* MiniGrid - A partially observable 2D grid-world environment.

* Entities - Agent, ball, box, door, keys

* Actions - pick, drop or move objects, unlock doors etc.

### Baby Language

* Synthetic Language (a proper subset of English) - Used to give instructions to the agent

* Support for verifying if the task (and the subtasks) are completed or not

### Levels

* A level is an instruction-following task. 

* Formally, a level is a distribution of missions - a combination of initial state of the environment and an instruction (in Baby Language)

* Motivated by curriculum learning, the authors create a series of tasks (with increasing difficulty).

* A subset of skills (competencies) is required for solving each task. The platform takes into account this constraint when creating a level.

### Heuristic Expert

* The platform supports a Heuristic expert that simulates the role of a human teacher and knows how to solve each task.

* For any level, it can suggest actions or generate demonstrations (given the state of the environment).

## Experiment

* An imitation learning baseline is trained for each level.

* Data requirement for each level and the benefits of curriculum learning and imitation learning are investigated (in terms of sample efficiency).

## Model Architecture

* GRU to encode the sentence, CNN to encode the input observation

* FiLM layer to combine the two representations

* LSTM to encode the per-timestep FiLM encoding (timesteps in the environment)

* Two model variants are considered:

    * Large Model - Bidirectional GRU + attention + large hidden state

    * Small Model - Unidirectional GRU + No attention + small hidden state

* Heuristic expert used to generate trajectory and the models are trained by imitation learning (to be used as baselines)

## Results

* The key takeaway is that the current deep learning approaches are extremely sample inefficient when learning a compositional language.

* Data efficiency of RL methods is much worse than that of imitation learning methods showing that the current imitation learning and reinforcement learning methods scale and generalize poorly.

* Curriculum-based pretraining and interactive learning was found to be useful in only some cases.

