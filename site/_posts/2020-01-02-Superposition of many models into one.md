---
layout: post
title: Superposition of many models into one
comments: True
excerpt: 
tags: ['2019', 'Continual Learning', 'Lifelong Learning', AI, CL, LL]

---


## Introduction

* The paper proposes a technique (called Parameter Superposition or PSP) for training and storing multiple models within a single set (or instance) of parameters.

* The different models exist in "superposition" and can be retrieved dynamically given task-specific context information.

* [Link to the paper](https://arxiv.org/abs/1902.05522).

## Parameter Substitution

* Consider a task with input $$x \in R^N$$ and parameter $$W$ \in R^{M \times N}$$ where the output (target or features) are given as $$y=Wx$$.

* Now consider $$K$$ such tasks with parameters $$W_1, W_2, \cdots W_K$$.

* If each $$W_k$$ requires only a small subspace in $$R^N$$, then a linear transformation $$C_k^{-1}$$ can be used such that each $$W_kC_k^{-1}$$ occupies a mutually orthogonal subspace in $$R^N$$.

* The set of parameters $$W_1, \cdots W_K$$ can be represented by a single $$W^{M \times N}$$ by adding $$W_kC_k^{-1}$$.

* The parameter corresponding to the $$k^{th}$$ task can be retrived (with some noise) using the context $$C_k$$ as $$W^{~}_k = WC_k$$

* Even though the retrieval is noisy, the effect of noise is limited for the context vectors used in the paper.

* Finally, $$\widetilde(y) = \widetilde(W)_{k}x = (WC_{k})x = W(C_{k}x)$$

* Instead of learning $$K$$ separate models, only $$K$$ context vectors (along with 1 superimposed model) needs to be learned.

* The key assumption is that $$N$$ (in $$x \in R^N)$$ is large enough such that each $$W_k$$ requires only a small subspace of $$R^N$$. 

* Since images and speech signals tend to occupy a low dimensional manifold, this requirement can be satisfied by over-parameterizing x.

## Choice of Context C

* Rotational Superposition (pspRotation)

    * Sample rotations uniformly from the orthogonal group $$O(M)$$.

    * Downside is that if $$M \sim N$$, it requires storing as many parameters as learning $$K$$ individual models (since $$C$$ is of the size of ##M \times M$$).

* Complex Superposition (pspComplex)

    * The design of rotational superposition can be improved by choosing $$C_k$$ to be a diagonal matrix ie $$C_k = diag(c_k)$$ where $$c_k$$ is a vector of size $$M$$.

    * Choosing $$c_k$$ to be a vector of complex numbers (of the form $$c_{k}^{j} = e^{i\phi_{j}(k)}$$ where $$\phi_{j}(k)$$ or the phase is sampled uniformly from $$[-\pi, \pi]$$) leads to $$C_k$$ being a digonal orthogonal matrix.

* Powers of a single context

    * The memory footprint can be further reduced by choosing the context vectors to be integral powers of the first context vector.

* Binary Superposition (pspBinary)
    
    * This is a special case of complex superposition where the context vectors are binary.

## Neural Network Superposition

* The parameter superposition principle can be applied to all the linear layers of a network.

* For the convolutional layers, it makes more sense to apply superposition to the convolutional kernel and not to the input image (as the dimensionality of convolutional parameters is smaller than that of inputs).

## Experiments

* For all the experiments, the baseline is a standard supervised learning setup, unless mentioned otherwise.

* The metric is the performance on the previous tasks when the model has been trained on the newer tasks.

* Input Interference
    
    * The input distribution changes over time.

    * Permuted MNIST dataset is used where each permutation of the pixels corresponds to a new task.

    * A new task is sampled every 1000 mini-batches.

    * As the network size increases, the performance of Parameter Superposition (psp) outperforms the baseline significantly.

    * pspRotation > pspComplex > pspBinary in terms of both performance and the number of additional parameters required for each new task.

    * Given that pspBinary is the easiest to implement while being comparable to more sophisticated baselines like Elastic Weight Consolidation (EWC) and Synaptic Intelligence, the paper presents most of the results with the pspBinary model.

* Continous Domain Shift

    * Rotating-MNIST and Rotating-FashionMNIST tasks are proposed to simulate continuous domain shift.

    * In these tasks, the input images are rotated in-plane by a small angle such that the rotation is complete after 1000 steps.

    * A new context is assigned after 100 steps as per step changes in the angle would be very small.

    * The 10 context vectors used in the first 1000 steps are reused for the subsequent steps.

* Randomly changing the context vector

    * The paper considers an ablation where the context vector is randomly changed at every step (of the 1000 step cycle). This required the superposition model to store 1000 models.

    * This approach is better than the supervised learning baseline but not as good as the proposed psp* models.

* Output Interference
    
    * This is the setup where the model transitions from one classification task to another.

    * Incremental CIFAR dataset is used with Resnet18 as the base model.

    * Baseline is a standard supervised learning model where a new classification head is used for each task (since the classes have a different meaning in each dataset). The model component before the classification layer is shared across the tasks.

    * Even though the labels are different across the datasets, the pspBinary model, trained with a single output layer, outperforms the multi-headed baseline.