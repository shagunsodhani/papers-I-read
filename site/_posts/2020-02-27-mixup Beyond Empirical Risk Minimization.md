---
layout: post
title: mixup - Beyond Empirical Risk Minimization 
comments: True
excerpt: 
tags: ['2017', 'Adversarial Robustness', 'Data Augmentation', ICLR 2018', AI, ERM, ICLR, Robustness]

---

## Introduction

* The paper proposes a simple and dataset-agnostic data augmentation mechanism called *mixup*.

* [Link to the paper]()

* Consider two training examples, $(x_1, y_1)$ and $(y_1, y_2)$, where $x_1$ and $x_2$ are the datapoints and $y_1$ and $y_2$ are the labels. 

* New training examples of the form $(\lambda \times x_1 + (1-\lambda) \times x_2, \lambda \times y_1 + (1-\lambda) \times y_2)$ are constructured by considering the linear interpolation of the datapoints and the labels. Here $\lambda \in [0, 1]$.

* $\lambda$ is sampled from a Beta distribution $Beta(\alpha, \alpha)$ where $\alpha \in (0, \infty)$.

* Setting $\lambda$ to 0 or 1 eliminates the effect of *mixup*. 

* Mixup encourages the neural network to favor linear behavior between the training examples.


## Experiments

* **Supervised Learning**

    * ImageNet for ResNet-50, ResNet-101 and ResNext-101.

    * CIFAR10/CIFAR100 for PreAct ResNet-18, WideResNet-28-10 and DenseNet.

    * Google command dataset for LeNet and VGG.

* In all these setups, adding *mixup* improves the performance of the model.

* *Mixup* makes the model more robust to noisy labels. Moreover, *mixup* + dropout improves over *mixup* alone. This hints that *mixup*'s benefits are complementary to those of dropout.

* *Mixup* makes the network more robust to adversarial examples in both white-box and black-box settings (ImageNet + Resnet101).

* *Mixup* also stabilizes the training of GANs by acting as a regularizer for the gradient of the discriminator. 


## Observations

* Convex combination of three or more examples (with weights sampled from a Dirichlet distribution) does not provide gains over the case of two examples.

* In the authors' implementation, *mixup* is applied between images of the same batch (after shuffling).

* Interpolating only between inputs, with the same labels, did not lead to the same kind of gains as *mixup*.