---
layout: post
title: Continual learning with hypernetworks
comments: True
excerpt: 
tags: ['2019', 'Continual Learning', 'ICLR 2020', 'Lifelong Learning', AI, CL, HyperNetwork, ICLR, LL]

---

## Introduction

* The paper proposes the use of task-conditioned [HyperNetworks](https://shagunsodhani.com/papers-I-read/HyperNetworks) for lifelong learning / continual learning setups.

* The idea is, the HyperNetwork would only need to remember the task-conditioned weights and not the input-output mapping for all the data points.

* [Link to the paper](https://arxiv.org/abs/1906.00695)

* [Author's Implementation](https://github.com/chrhenning/hypercl)

## Terminology

* $f$ denotes the network for the given $t^{th}$ task.

* $h$ denotes the HyperNetwork that generates the weights for $f$.

* $\Theta_{h}$ denotes the parameters of $h$.

* $e^{t}$ denotes the input task-embedding for the $t^{th}$ task.

## Approach

* When training on the $t^{th}$ task, the HyperNetworks generates the weights for the network $f$. 

* The current task loss is computed using the generated weights, and the candidate weight update ($\Delta \Theta_{h}$) is computed for $h$.

* The actual parameter change is computed by the following expression:

$L_{total} = L{task}(\Theta_{h}, e^{T}, X^{T}, Y^{T}) + \frac{\beta_{output}}{T-1} \sum_{t=1}^{T-1} \| f_{h}(e^{t}, \Theta_{h}^*) - f_{h}(e^{(t)}, \Theta_{h} + \Delta \Theta_{h} ))\|^2$

* $L_{task}$ is the loss for the current task.

* $(X^{T}, Y^{T})$ denotes the training datapoints for the $T^{th}$ task. 

* $\beta_{output}$ is a hyperparameter to control the regularizer's strength.

* $\Theta_{h}^*$ denotes the optimal parameters after training on the $T-1$ tasks.

* $\Theta_{h} + \Delta \Theta_{h}$ denotes the one-step update on the current $h$ model.

* In practice, the task encoding $e^{t}$ is chunked into smaller vectors, and these vectors are fed as input to the HyperNetwork.

* This enables the HyperNetwork to produce weights iteratively, instead of all at once, thus helping to scale to larger models.

* The paper also considers the problem of inferring the task embedding from a given input pattern.

* Specifically, the paper uses task-dependent uncertainty, where the task embedding with the least predictive uncertainty is chosen as the task embedding for the given unknown task. This approach is referred to as HNET+ENT.

* The paper also considers using HyperNetworks to learn the weights for a task-specific generative model. This generative model will be used to generate pseudo samples for rehearsal-based approaches. The paper considers two cases:

    * HNET+R where the replay model (i.e., the generative model) is parameterized using a HyperNetwork.

    * HNET+TIR, where an auxiliary task inference classifier is used to predict the task identity.

## Experiments

* Three setups are considered

    * CL1 - Task identity is given to the model.

    * CL2 - Task identity is not given, but task-specific heads are used.

    * CL3 - Task identity needs to be explicitly inferred.

* On the permuted MNIST task, the proposed approach outperforms baselines like Synaptic Intelligence and Online EWC, and the performance gap is more significant for larger task sequences.

* Forward knowledge transfer is observed with the CIFAR datasets.

* One potential limitation (which is more of a limitation of HyperNetworks) is that HyperNetworks may be harder to scale for larger models like ResNet50 or transformers, thus limiting their usefulness for lifelong learning use cases.