---
layout: post
title: Towards a natural benchmark for continual learning
comments: True
excerpt: 
tags: ['2018', 'Catastrophic Forgetting', 'Continual Learning', 'Lifelong Learning', 'NeurIPS 2018', 'NeurIPS Workshop 2018', AI, CL, Workshop]
---

## Introduction

* Continual Learning paradigm focuses on learning from a non-stationary stream of data with additional desiderata - transferring knowledge from previously seen task to unseen tasks and being resilient to catastrophic forgetting - all with a fixed memory and computational budget.

* This is in contrast to the IID (independent and identically distributed) assumption in statistical learning.

* One common example of the non-iid data is setups involving sequential decision making - eg Reinforcement learning.

* [Paper](https://marcpickett.com/cl2018/CL-2018_paper_48.pdf)

## Benchmark

* Many existing benchmarks use MNIST as the underlying dataset (eg Permuted MNIST, Split MNIST, etc). These benchmarks lack complexity and make it hard to observe positive and negative backward transfer.

* Most works focus only on the catastrophic forgetting challenge and ignore the other issues (like computation and memory footprint, the capacity of the network, etc).

* The paper proposes a new benchmark based on Starcraft II video game to understand the different approaches for lifelong learning.

* The sequence of tasks is designed to be a curriculum - the learning agent stats with learning simple skills and later move to more complex tasks. These complex tasks require remembering and composing skills learned in the earlier levels.

* To evaluate for catastrophic forgetting, the tasks are designed such that not all the skills are needed for solving each task. Hence the learning agent needs to remember skills even though they are not needed at the current level.

* Each level comes with a fixed computational budget of episodes and each episode has a fixed time limit. Once the budget is consumed the agent has to proceed to the next level. Hence agents with better sample efficiency would benefit.

* The benchmark supports both RL and supervised learning version. In the supervised version, expert agents (pretrained on each level) are also provided.

* Baselines are provided for distillation (using experts): sequential training (fine tuning), Dropout and SER. None of the baseline methods achieve positive or negative backward transfer.

* When modeled as a pure RL task, the benchmark is extremely difficult to solve.

* The paper suggests using a metric to record the amount of learning/data required to recover performance on the previous task.