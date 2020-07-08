---
layout: post
title: When to use parametric models in reinforcement learning? 
comments: True
excerpt: 
tags: ['2019', 'Deep Reinforcement Learning', 'Model-Based', 'Model-Free', 'Neurips 2019', 'Reinforcement Learning', AI, DRL, Neurips, Planning, RL]

---

## Introduction

* The paper compares replay-based approaches with model-based approaches in Reinforcement Learning (RL).

* It hypothesizes that if the parametric model is only used for generation transitions for the update rule, then under certain conditions, replay-based approaches will be as good as model-based approaches.

* [Link to the paper](https://arxiv.org/abs/1906.05243)

## Terminology

* Planning: Any algorithm that uses additional computations (but not additional experience) to improve its performance.

* Learning: Any algorithm that uses additional experience to improve its performance.

* In some cases, a replay buffer can be seen as a model. For example, querying using state-action pair (from the replay buffer) is similar to querying the (expected) next-state and reward from a model. In general, the model will be more flexible as any arbitrary state-action pair can be used for querying.

## Computation Properties

* Parametric models require more computation than sampling from a replay buffer. In contrast, the cost of maintaining a replay buffer scales linearly with their capacity.

* Parametric models are useful for planning multiple-steps into the future while it is much harder to do so with a replay buffer (even more so with pixel observations).

* An imperfect model maybe be more suitable for selecting actions (instead of updating the policy) because the chosen action, when executed in the environment, will lead to transitions that would improve the model.

* When planning with an imperfect model, it is better to plan backward, as the update is applied on an imaginary state (which would not be encountered if the model is poor). 

* If the model is accurate, forward and backward planning is equivalent. This distinction between forward and backward updates does not apply to replay buffers.

## Failure to learn

* When using a replay buffer and (i) uniformly replaying transitions, (ii) from a buffer containing only full episodes, and (iii) using TD updates, then the algorithm is stable.

* When using a replay buffer and (i) uniformly replaying transitions, (ii) generating transitions using a model, and (iii) using TD updates, then the algorithm can diverge.

* This case can be fixed by:
    
    * Repeatedly interating over the model and sampling transitions *to* and *from* the state model generates (not a satisfactory solution). 

    * Using multiple-step returns (this can increase the variance).

    * Use algorithms specifically for stable off-policy learning (not a definitive solution).

## Model-based algorithms at scale

* The paper compares against SimPLe (model-based) with Rainbow DQN (replay-based).

* The paper shows that when using a similar number of real interactions, Rainbow DQN needs fewer replay samples than model samples in SimPLe, making it more efficient (computation-wise).

* Changes to Rainbow DQN: 
	* Increase number of steps, for bootstrapping, from 3 to 20.
	* Reduce the number of steps, before sampling starts from the replay buffer, from 20K to 1600.

* With these changes, Rainbow DQN outperforms SimPLe in 17 out of 26 games.

## Conclusion

* When using a parametric model in a replay-like setting (sampling observed states from the past), model-based learning can be unstable (in theory). Using a replay buffer is likely a better strategy under the state sampling distribution.

* Parametric models are likely more useful when:
	* planning backward for credit assignment - even if the model is in-accurate, backward planning will only update fictional states.
	* planning forward for behavior - the resulting plan is only used to collect real *experience* in the environment (and not directly update the policy).