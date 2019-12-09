---
layout: post
title: Gossip based Actor-Learner Architectures for Deep RL
comments: True
excerpt: 
tags: ['2019', 'Deep Reinforcement Learning', 'Distributed Reinforcement Learning', 'Neurips 2019', 'Reinforcement Learning', AI, DRL, Neurips, RL]

---

* [Link to the paper](https://arxiv.org/abs/1906.04585)

* The paper considers the task of training an RL system by sampling data from multiple simulators (over parallel devices).

* The setup is that of distributed RL setting with *n* agents or actor-learners (composed of a single learner and several actors). These agents are trying to maximize a common value function.

* One (existing) approach is to perform on-policy updates with a shared policy. The policy could be updated in synchronous (does not scale well) or asynchronous manner (can be unstable due to stale gradients).

* Off policy approaches allow for better computational efficiency but can be unstable during training.

* The paper proposed Gossip based Actor-Learner Architecture (GALA) which uses asynchronous communication (gossip) between the *n* agents to improve the training of Deep RL models.

* These agents are expected to converge to the same policy.

* During training, the different agents are not required to share the same policy and it is sufficient that the agent's policies remain $\epsilon$-close to each other. This relaxation allows the policies to be trained asynchronously.

* GALA approach is combined with A2C agents resulting in GALA-A2C agents. They have better computational efficiency and scalability (as compared to A2C) and similar in performance to A3C and Impala.

* Training alternates between one local policy-gradient (and TD update) and asynchronous gossip between agents. 

* During the gossip step, the agents send their parameters to some of the other agents (referred to as the peers) and update their parameters based on the parameters received from the other agents (for which the given agent is a peer).

* GALA agents are implemented using non-blocking communication so that they can operate asynchronously.

* The paper includes the proof that the policies learned by the different agents are within $\epsilon$ distance of each other (ie all the policies lie within an $\epsilon$-distance ball) thus ensuring that the policies do not diverge much from each other.

* Six games from the Ataru 2600 games suite are used for the experiments.

* Baselines: A2C, A3C, Impala

* GALA agents are configured in a directed ring graph topology.

* With A2C, as the number of simulators increases, the number of convergent runs (runs with a threshold reward) decreases. 

* Using gossip algorithms increases or maintains the number of convergent runs. It also improves the performance, sample efficiency and compute efficiency of A2C across all the six games.

* When compared to Impala and A3C, GALA-A2C generally outperforms (or performs as well as) those baselines.

* Given that the learned policies remain within an $\epsilon$ ball, the agent's gradients are less correlated as compared to the A2C agents.