---
layout: post
title: Decentralized Reinforcement Learning -- Global Decision-Making via Local Economic Transactions
comments: True
excerpt: 
tags: ['2020', 'Credit Assignment', 'Decentralized Reinforcement Learning', 'ICML 2020', 'Hierarchical Reinforcement Learning', 'Reinforcement Learning', AI, Economics, HRL, ICML, RL]


---

## Introduction

* The paper explores the connections between the concepts of a single agent vs. society of agents.

* A society of agents can be modeled as a single agent while a single agent can be modeled as a society of components (or sub-agents).

* The paper focuses on mechanisms for training a society of self-interested agents to solve a given task -- as if the system was a single task.

* [Link to the paper](https://arxiv.org/abs/2007.02382)

## Contributions

* **Societal-decision making** framework relates the local optimization problem of a single agent with the global optimization problem of a society of agents.

* **Cloned Vickrey Society** is proposed as a mechanism to guarantee that an agent’s dominant strategy equilibrium coincides with the group’s optimal policy.

* A class of **decentralized RL algorithms** that optimize the MDP object of the society as a whole, as a consequence of individual agents optimizing their objectives.

* Empirical evaluation of Cloned Vickrey Society using any implementation called **Credit Conserving Vickery**.

## Terminology

* *Environment* - a tuple that specifies an input space, an output space, and parameters for determining an objective.
	
	* A standard RL setup can be mapped to *environment* by mapping state space to input space, action space to output space and reward function, transition function, and discount factors to the parameters specifying the objective.

* *Agent* - a function that maps input space to output space.

* *Objective* - a functional that maps an agent to a real number.

* In *auction environments*, the input space is a single auction item (say *s*), and the output space is bidding space *B*.

* There are *N* agents who compete by bidding for an item *s* using their bidding policy.

* $b$ is a vector of bids produced by the agents.

* $v_s$ is a vector of agent’s valuations of item *s*.

* The $i^{th}$ agent’s utility is given as $v_s^i \times X^i(b) - P^i(b)$. Here, $X^i(b)$ is the portion of $s$ allocated to $i^{th}$ agent and $P^i(b)$ is the price that $i^{th}$ agent is willing to pay.

## Design Choices

* Each agent is independently maximizing its utility.

* In certain conditions (i.e., if the auction is dominant strategy incentive compatible), it is optimal for each agent to bid its valuation.

* These conditions are satisfied by the Vickery auction where $P^i(b)$ is set to be the second-highest bid and $X^i(b) = 1$ if the $i^{th}$ agent wins (and 0 otherwise).

* A *society* is a set of agents where each agent is a tuple of bidding policy $\psi$ and a transformation function.

* The environment is modeled at two levels - (i) global environment (referred to as the global MDP) and local environment (referred to as local auction).

* Each state $s$ in the global MDP is an auction item in a different auction. The winner (of local auction at $s$) transforms $s$ into some other state $s’$.

* If these transformations are modeled as actions, then the proposed framework can be interpreted as a decentralized reinforcement learning framework.

* Motivated by the design of market economy (where economic transactions determine wealth distribution), the paper proposes that, for an agent, the valuation of winning an auction is the revenue it can receive in the auction at the next timestep by selling the transformed state.

* A global MDP that adhere to this design is referred to as the Market MDP.

* There is a catch in the design of the market MDP - the winning agent, at time $t-1$, gets the amount that the highest bidder is willing to pay at time $t+1$. But the winner at time $t+1$ only paid the second-highest bid. Hence, the credit is not conserved.

* This inconsistency can be fixed by introducing “duplicate” (or cloned) agents, and the society is called the Cloned Vickery Society.

* The Cloned Vickrey Auction mechanism is compared against alternate bidding mechanisms like *first price auction* (where winner pays the bid they proposed), solitary version of Vickrey auction (no cloning), and *Environment Reward* where only environment reward is used, and there is no price term.

* It is empirically shown that Cloned Vickrey Auction learns bids that are most close to their actual valuations. Moreover, solitary version leads bids which are more spread out than the ones learned by cloned version. This highlights the importance of competitive pressure to learn bid values.

* Three different implementations of Cloned Vickrey Auction are considered:
	
	* Bucket Brigade (BB) - winner at timestep $t$ receives the highest bid at time step $t+1$, and the subsequent winner pays the highest bid. This case satisfies Credit Conservation and Bellman Optimality.

	* Vickrey (V) - winner at timestep $t$ receives the highest bid at time step $t+1$, and the subsequent winner pays the second-highest bid. This case satisfies Truthful Dominant Strategy and Bellman Optimality.

	* Credit Conserving Vickrey (CCV) - winner at timestep $t$ receives the second-highest bid at time step $t+1$, and the subsequent winner pays the second-highest bid. This case satisfies Truthful Dominant Strategy and Credit Conservation.

* CCV implementation provides bid values closest to the optimal Q-values.

* In one experiment, the paper explores the use of the proposed approach for selecting between sub-policies. It shows that CVV is more sample efficient for pretraining sub-policies and adapting them to transfer tasks.

* In another experiment, the task is to transform MNIST images by composing two out of 6 affine transformations. The transformed images are fed to a pretrained classifier that predicts a label. The agent gets a reward of 1 if the classifier makes correct prediction and 0 otherwise. CCV implementation obtains a mean reward of 0.933, thus highlighting the effectiveness of the CCV model.