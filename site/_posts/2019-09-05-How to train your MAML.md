---
layout: post
title: How to train your MAML
comments: True
excerpt: 
tags: ['2018', 'Empirical Advice', 'ICLR 2019', 'Meta Learning', AI, ICLR, MAML]

---

## Introduction

* The paper proposes MAML++ - a modification of MAML algorithm that stabilizes its training improves generalization performance and reduces the computational overhead.

* [Link to the paper](https://arxiv.org/abs/1810.09502)

## Notes

### Unstable Training

* Training the outer loop requires unfolding the inner loop multiple times. 

* In absence of skip connections, the gradient is multiplied by the same parameter multiple times. 

* Large depth and absent skip connections could lead to exploding and vanishing gradients respectively.

* The paper proposes to stabilize the gradient propagation by minimizing the target set loss computed by the base-network after every step towards a support set task.

* It is important to anneal the contribution of earlier steps and increase the contribution of later steps over time.

### Second Order derivatives are expensive to compute

* While the first-order MAML is faster, the resulting model may not have as good a generalization error as the second-order MAML.

* The paper proposes to use derivative order annealing where the first order gradients are used for the first 50 epochs and the network uses second-order gradients from thereon.

* This derivative order annealing appears to be more stable than models that use second-order derivatives only.


### Batch Normalization

* In MAML, the statistics of the current batch are used for normalization instead of accumulating the running statistics.

* The paper proposes to collect the statistics per step which can increase the convergence speed, stability, and generalization performance.

* In MAML, the batch normalization biases are not updated in the inner-loop which can adversely impact the performance.

* The paper proposes to learn a set of biases (per step) within the inner loop update.

### Fixed Learning Rate

* MAML uses a single learning rate across all the steps and all the parameters. This means there is one single learning rate that needs to be hyperparameter to work well for all the layers and steps.

* An alternate solution would be to learn a separate learning rate per parameter but this can be impractical as it doubles the number of parameters to be learned.

* The paper proposes to learn a learning rate and direction for each layer in the network, for each step it takes in the inner loop.

* The paper also proposed to anneal the learning rate of the outer loop (using cosine annealing) as it helps to achieve better generalization.

## Results

* Using these modifications helps to outperform the MAML model on both Omniglot and MiniImagenet datasets.

* The biggest benefit comes by learning the per-layer, per-step learning rates and by using the per-step batch normalization.