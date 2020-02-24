---
layout: post
title: Rapid Learning or Feature Reuse? Towards Understanding the Effectiveness of MAML
comments: True
excerpt: 
tags: ['2019', 'ICLR 2020', 'Meta Learning', AI, ICLR, MAML]

---


## Introduction

* The paper investigated two possible reasons behind the usefulness of MAML algorithm:

    * **Rapid Learning** - Does MAML learn features that are amenable for rapid learning?

    * **Feature Reuse** - Does the MAML initialization provide high-quality features that are useful for the unseen tasks.

* This leads to a follow-up question: how much task-specific inner loop adaptation is needed.

* [Link to the paper](https://arxiv.org/abs/1909.09157)

## Approach

* In a standard few-shot learning setup, the different datasets have different classes. Hence, the top-most layer (or the head) of the learning model should be different for different tasks.

* The subsequent discussion only applies to the body of the network (ie, network minus the head).

* **Freezing Layer Representations**
    
    * In this setup, a subset (or all) of parameters are frozen (after MAML training) and are not adapted during the representation.

    * Even when the entire network is frozen, the performance drops only marginally.

    * This indicates that the representation learned by the meta-initialization is good enough to be useful on the test tasks (without requiring any adaptation step).

    * Note that the head of the network is still adapted during testing.

* **Representational Similarity**
    
    * In this setup, the paper reports the change in the latent representation (learned by the network) during the inner loop update with a fully trained model.

    * Canonical Correlation Analysis (CCA) and Central Kernel Alignment (CKA) metrics are used to measure the similarity between the representations.

    * The main finding is that the representations in the body of the network are very similar before and after the inner loop updates while the representations in the head of the network are very different.

* The above two observations indicate that feature reuse is the primary driving factor for the success of MAML.

* **When does feature reuse happen**

    * The paper considers the model at different stages of training and compares the similarity in the representation (before and after the inner loop update).

    * Even early in training, the CCA similarity between the representations (before and after the inner loop update) is quite high. Similarly, freezing the layers (for the test time update), early in training, does not degrade the test time performance much. This hints that the feature reuse happens early in the learning process.

## The ANIL (Almost No Inner Loop) Algorithm

* The empirical evidence suggests that the success of MAML lies in the feature reuse.

* The authors build on this observation and propose a simplification of the MAML algorithm: ANIL or Almost No Inner Loop Algorithm

* In this algorithm, the inner loop updates are applied only to the head of the network.

* Despite being much more straightforward, the performance of ANIL is close to the performance of MAML for both few-shot image classification and RL tasks.

* Removing most of the inner loop parameters speed up the computation by a factor of 1.7 (during training) and 4.1 (during inference).

## Removing the Inner Loop Update

* Given that it is possible to remove most of the parameters from the inner loop update (without affecting the performance), the next step is to check if the inner loop update can be removed entirely.

* This leads to the NIL (No Inner Loop) algorithm, which does not involve any inner loop adaptation steps.

### Algorithm
    
* A few-shot learning model is trained - either with MAML or ANIL.

*  During testing, the head is removed.

* For each task, the K training examples are fed to the body to obtain class representations.

* For a given test data point, the representation of the data point is compared with the different class representations to obtain the target class.

* The NIL algorithm performs similar to the MAML and the ANIL algorithms for the few-shot image classification task.

* Note that it is still important to use MAML/ANIL during training, even though the learned head is not used during evaluation.

## Conclusion

* The paper discusses the different classes of meta-learning approaches. It concludes with the observation that feature reuse (and not rapid adaptation) seems to be the common model of operation for both optimization-based meta-learning (e.g., MAML) and model-based meta-learning.