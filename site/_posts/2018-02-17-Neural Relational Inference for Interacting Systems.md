---
layout: post
title: Neural Relational Inference for Interacting Systems
comments: True
excerpt: Neural Relational Inference for Interacting Systems
tags: ['2018', 'Dynamical System', 'Graph Representation', 'Latent Variable', 'Relational Inference', AI, Graph, GNN, VAE]
---

## Introduction

* The paper presents Neural Relational Inference (NRI) model which can infer underlying interactions in a dynamical system in an unsupervised manner, using just the observational data in terms of the trajectories.

* For instance, consider a simulated system where the particles are connected to each other by springs. The observational data does not explicitly specify which particles are connected to each other and only contains information like position and velocity of each particle at different timesteps.

* The task is to explicitly infer the interaction structure (in this example, which pair of particles are connected to each other) while learning the dynamical model of the system itself.

* [Link to the paper](https://arxiv.org/abs/1802.04687)

* [Link to the implementation](https://github.com/ethanfetaya/nri)

## Model

* The model consists of an encoder that encodes the given trajectories into an interaction graph and a decoder that decodes the dynamical model given the interaction graph.

* The model starts by assuming that a full connected interaction graph exists between the objects in the system.

* For this latent graph **z**, *z<sub>i, j</sub>* denotes the (discrete) edge type between object *v<sub>i</sub>* and *v<sub>j</sub>* with the assumption that there are *K* edge types.

* The object *v<sub>i</sub>* has a feature vector *x<sub>i</sub><sup>t</sup>* associated with it at time *t*. This feature vector captures information like location and velocity.

### Encoder

* A Graph Neural Network (GNN) acts on the fully connected latent graph *z*, performs message passing from node to node via edges and predicts the discrete label for each edge.

* The GNN architecture may itself use MLPs or ConvNets and returns a factorised distribution over the edge types *q<sub>&phi;</sub>(z\|x)*.

### Decoder

* The decoder is another GNN (with separate params for each edge type) that predicts the future dynamics of the system and returns *p<sub>&theta;</sub>(x\|z)*.

* The overall model is a VAE that optimizes the ELBO given as:
* E<sub>q<sub>&phi;</sub>(z\|x)</sub>[log p<sub>&theta;</sub>(x\|z)] − KL[q<sub>&phi;</sub>(z\|x)\|\|p<sub>&theta;</sub>(z)] 

* *p<sub>&theta;</sub>(x)* is the prior which is assumed to be uniform distribution over the edge types.

* Instead of predicting the dynamics of the system for just the next timestep, the paper chooses to use the prediction multiple steps (10) in the future. This ensures that the interactions can have a significant effect on the dynamics of the system.

* In some cases, like real humans playing a physical sport, the dynamics of the system need not be Markovian and a recurrent decoder is used to model the time dependence.

## Pipeline

* Given the dynamical system, run the encoder to obtain *q<sub>&phi;</sub>(z\|x)*.

* Sample *z<sub>i, j</sub>* from *q<sub>&phi;</sub>(z\|x)*.

* Run the decoder to predict the future dynamics for the next T timesteps.

* Optimise the ELBO loss.

* Note that since the latent variables (edge labels) are discrete in this case, the sampling is done from a continuous approximation of the discrete distribution and reparameterization trick is applied over this discrete approximation to get the (biased) gradients.


## Observations

* Experiments are performed using simulated systems like particles connected to springs, phase coupled oscillators and charged particles and using real-world data like CMU Motion Capture database and NBA tracking data.

* The NRI system effectively predicts the dynamics of the systems and is able to reconstruct the ground truth interaction graph (for simulated systems).