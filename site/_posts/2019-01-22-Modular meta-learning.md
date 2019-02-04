---
layout: post
title: Modular meta-learning
comments: True
excerpt: 
tags: ['2018', 'Meta Learning', 'Modular Meta Learning', 'Modular ML',  'Modular Network', Module]
---

## Introduction

* The paper proposes an approach for learning neural networks (modules) that can be combined in different ways to solve different tasks (combinatorial generalization).

* The proposed model is called as BOUNCEGRAD.

* [Link to the paper](https://arxiv.org/abs/1806.10166)

* [Link to the code](https://github.com/FerranAlet/modular-metalearning)

## Setup

* Focuses on supervised learning.

* Task distribution *p(T)*.

* Each task is a joint distribution *p<sub>T</sub>(x, y)* over *(x, y)* data pairs.

* Given data from *m* meta-training tasks, and a meta-test task, find a hypothesis *h* which performs well on the unseen data drawn from the meta-test task.

## Structured Hypothesis

* Given a compositional scheme *C*, a set of modules *F<sub>1</sub>, ..., F<sub>k</sub>* (represented as a whole by *F*) and the set of their respective parameters &theta;<sub>1</sub>, ..., &theta;<sub>k</sub> (represented as a whole by &theta;), *(C, F, &theta;)* represents the set of possible functional input-output mappings. These mappings form the hypothesis space.

* A structured hypothesis model is specified by what modules to use and their parametric forms (but not the values).

### Examples of compositional schemes

* Choosing a single module for the task at hand.

* Fixed compositional structure but different modules selected every time.

* Weight ensemble (maybe using attention mechanism)

* General function composition tree

### Phases

* Offline Meta Learning Phase:
    
    * Take training and validation dataset for the first *k* tasks and generate a parameterization for each module *&theta;<sub>1</sub>, ..., &theta;<sub>k</sub>*.

    * The hypothesis (or composition) to use comes from the online meta-test learning phase.

    * In this stage, find the best &theta; given a structure.

* Online Meta-test Learning Phase

    * Given a hypothesis space and &theta;, the output is a compositional form (or hypothesis) that specifies how to compose the models.

    * In this stage, find the best structure, given a hypothesis space and &theta;.

## Learning Algorithm

* During Meta-test learning phase, simulated annealing is used to find the optimal structure, with temperature *T* decreased over time.

* During meta-learning phrase, the actual objective function is replaced by a surrogate, smooth objective function (during the search step) to avoid local minima.

* Once a structure has been picked, any gradient descent based approach can be used to optimize the modules.

* Basically the state of optimization process comprises of the parameters and the temperature. Together, they are used to induce a distribution over the structures. Given a structure, &theta; is optimized and *T* is annealed over time.

* The learning procedure can be improved upon by performing parameter tuning during the online (meta-test learning) phase as well. the resulting approach is referred to as MOMA - MOdular MAml.

## Experiments

### Approaches

* Pooled - Single network using combined data of all the tasks.

* MAML - Single network using MAML

* BOUNCEGRAD - Modular Network without MAML adaptation in online learning.

* MOMA - BOUNCEGRAD with MAML adaptation in online learning.

### Domains

#### Simple Functional Relationships

* Sine-function prediction problem

* In general, MOMA outperforms other models.

* With a small amount of online training data, BOUNCEGRAD outperforms other models as it has a better structural prior.

#### Predicting next frame of a kinematic skeleton (motion capture data)

* 11 different objects (with different shapes) on 4 surfaces with different friction properties.

* 2 meta-learning scenarios are considered. In the first case, the object-surface combination in the test case was present in some meta-training tasks and in the other case, it was not present.

* For previously seen combinations, MOMA performs the best followed by BOUNCEGRAD and MAML. 

* For unseen combinations, all the 3 are equally good.

* Compositional scheme is the attention mechanism.

* An interesting result is that the modules seem to specialize (and activate more often) based on the shape of the object.

### Predicting next frame of a kinematic selection (using motion capture data)

* Composition Structure - generating kinematics subtrees for each body part (2 legs, 2 arms, 2 torsi).

* Again 2 setups are used - one where all activities in the training and the meta-test task are shared while the other setup where the activities are not shared.

* For known activities MOMA and BOUNCEGRAD perform the best while for unknown activities, MOMS performs the best.

## Notes

* While the approach is interesting, maybe a more suitable set of tasks (from the point of composition) would be more convincing.

* It would be useful to see the computational tradeoff between MAML, BOUNCEGRAD, and MOMA.
