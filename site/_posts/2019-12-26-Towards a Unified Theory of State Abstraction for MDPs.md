---
layout: post
title: Towards a Unified Theory of State Abstraction for MDPs
comments: True
excerpt: 
tags: ['2006', 'Markov Decision Process', 'Reinforcement Learning', 'State Abstraction', AI, MDP, RL]

---


## Introduction

* The paper studies five different techniques for stat abstraction in MDPs (Markov Decision Processes) and evaluates their usefulness for planning and learning.

* The general idea behind abstraction is to map the actual (or observed) state to an abstract state that should be more amenable for learning.

* It can be thought of as a mapping from one representation to another representation while preserving some useful properties.

* [Link to the paper](https://pdfs.semanticscholar.org/ca9a/2d326b9de48c095a6cb5912e1990d2c5ab46.pdf)


## General Definition

* Consider a MDP $$M = <S, A, P, R, \gamma>$$ where $$S$$ is the finite set of states, $$A$$ is finite set of actions, $$P$$ is the transition function, $$R$$ is the bounded reward function and $$\gamma$$ is the discount factor.

* The abstract version of the MDP is $$\widetilde{M} = <\widetilde{S}, A, \widetilde{P}, \widetilde{R}, \gamma>$$ where $$\widetilde{S}$$ is the finite set if abstract states, $$\widetilde{P}$$ is the transition function in the abstract state space and $$\widetilde{R}$$ is the bounded reward function in the abstract reward space.

* Abstraction function $$\phi$$ is a function that maps a given state $$s$$ to its abstract counterpart $$\widetilde{s}$$.

* The inverse image $$\phi^{-1}(\widetilde{s})$$ is the set of ground states that map to the $$\widetilde{s}$$ under the abstraction function $$\phi$$.

* A wieghing functioon $$w(s)$$ is used to measure how much does a state $$s$$ contribute to the abstract state $$\phi(s)$$.

## Topology of Abstraction Space

* Given two abstraction functions $$\phi_{1}$$ and $$\phi_{2}$$, $$\phi_{1}$$ is said to be *finer* than $$\phi_{2}$$ iff for any states $$s_{1}, s_{2}$$ if $$\phi_{1}(s_{1}) = \phi_{1}(s_{2})$$ then $$\phi_{2}(s_{1}) = \phi_{2}(s_{2})$$.

* This *finer* relation is reflex, antisymmetric, transitive and partially ordered.

## Five Types of Abstraction

* While many abstractions are possible, not all abstractions are equally important.

* Model-irrelevance abstraction $$\phi_{model}$$:

    * If two states $s_{1}$ and $s_{2}$ have the same abstracted state, then their one-step model is preserved.

    * Consider any action $$a$$ and any abstract state $$\widetilde{s}$$, if $$\phi_{model}(s_{1} = \phi_{model}(s_{2})$$ then $$R(s_1, a) = R(s_2, a)$$ and $$\sum_{s' \in \phi_{model}^{-1}\widetilde(s)}P_{s_1, s'}^{a} = \sum_{s' \in \phi_{model}^{-1}\widetilde(s)}P_{s_2, s'}^{a}$$.

* $$Q^{\pi}$$-irrelevance abstraction:
    
    * It preserves the state-action value finction for all the states.

    * $$\phi_{Q^{\pi}}(s_1) = \phi_{Q^{\pi}}(s_2)$$ implies $$Q^{\pi}(s_1, a) = Q^{\pi}(s_1, a)$$.

* $$Q^{*}$$-irrelevance abstraction:

    * It preserves the optimal state-action value function.

* $$a^{*}$$-irrelevance abstraction:

    * It preserves the optimal action and its value function.

* $$\phi_{\pi^{*}}$$-irrelevance abstraction:

    * It preserves the optimal action.

* In terms of *fineness*, $$\phi_0 \geq \phi_{model} \geq \phi_{Q^{\pi}} \geq \phi_{Q^*} \geq \phi_{a^*} \geq \phi_{\pi^*} $$. Here $$\phi_0$$ is the identity mapping ie $$\phi_0(s) = s$$

* If a property applies to any abstraction, it also applies to all the finer abstractions.

## Key Theorems

* As we go from finer to coarser abstractions, the information loss increases (ie fewer components can be recovered) while the state-space reduces (ie the efficiency of solving the problem increases). This leads to a tradeoff when selecting abstractions.

* For example, with abstractions $$\phi_{model}, \phi_{Q^{\pi}}, \phi_{Q^*}, \phi_{a^*}$$, the optimal abstract policy $$\widetilde(\pi)^*$$ is optimal in the ground MDP.

* Similarly, if each state-action pair is visited infinitely often and the step-size decays properly, Q-learning with $$\phi_{model}, \phi_{Q^{\pi}}, \phi_{Q^*}$$ converges to the optimal state-action value functions in the MDP. More conditions are needed for convergence in the case of the remaining two abstractions.

* For $$\phi_{model}, \phi_{Q^{\pi}}, \phi_{Q^*}, \phi_{a^*}$$, the model built with the experience converges to the true abstract model with infinite experience if the weighing function $$w(s)$$ is fixed.


