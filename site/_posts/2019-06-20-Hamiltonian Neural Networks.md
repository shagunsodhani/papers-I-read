---
layout: post
title: Hamiltonian Neural Networks
comments: True
excerpt: 
tags: ['2019', AI, Physics]

---

## Introduction

* The paper proposes a very cool idea at the intersection of deep learning and physics. 

* The idea is to train a neural network architecture that builds on the concept of Hamiltonian Mechanics (from Physics) to learn physical conservation laws in an unsupervised manner.

* [Link to the paper](https://arxiv.org/abs/1906.01563)

* [Link to the code](https://github.com/greydanus/hamiltonian-nn)

* [Link to author's blog](https://greydanus.github.io/2019/05/15/hamiltonian-nns/)

## Hamiltonian Mechanics

* It is a branch of physics that can describe systems which follow some conservation laws and invariants.

* Consider a set of *N* pair of coordinates [(q<sub>1</sub>, p<sub>1</sub>), ..., (q<sub>N</sub>, p<sub>N</sub>)] where **q** = [q<sub>1</sub>, ..., q<sub>N</sub>] dnotes the position of the set of objects while **p** = [p<sub>1</sub>, ..., p<sub>N</sub>] denotes the momentum of the set of variables. 

* Together these *N* pairs completely describe the system.

* A scalar function *H(**q**, **p**)*, called as the Hamiltonian is defined such that the partial derivative of *H* with respect to **p** is equal to derivative of **q** with respect to time *t* and the negative of partial derivative of *H* with respect to **q** is equal to derivative of **p** with respect to time *t*.

* This can be expressed in the form of the equation as follows:

<img src="https://raw.githubusercontent.com/shagunsodhani/papers-I-read/master/assets/HNN/equation1.png" alt="equation1" width="100" height="100" />

* The Hamiltonian can be tied to the total energy of the system and can be used in any system where the total energy is conserved.

## Hamiltonian Neural Network (HNN)

* The Hamiltonian *H* can be parameterized using a neural network and can learn conserved quantities from the data in an unsupervised manner.

* The loss function looks as follows:

<img src="https://raw.githubusercontent.com/shagunsodhani/papers-I-read/master/assets/HNN/equation2.png" alt="equation2" width="400" height="50" />

* The partial derivatives can be obtained by computing the *in-graph* gradient of the output variables with respect to the input variables.

## Observations

* For setups where the energy must be conserved exactly, (eg ideal mass-spring and ideal pendulum), the HNN learn to preserve an energy-like scalar.

* For setups where the energy need not be conserved exactly, the HNNs still learn to preserve the energy thus highlighting a limitation of HNNs.

* In case of two body problems, the HNN model is shown to be much more robust when making predictions over longer time horizons as compared to the baselines.

* In the final experiment, the model is trained on pixel observations and not state observations. In this case, two auxiliary losses are added: auto-encoder reconstruction loss and a loss on the latent space representations. Similar to the previous experiments, the HNN model makes robust predictions over much longer time horizons.