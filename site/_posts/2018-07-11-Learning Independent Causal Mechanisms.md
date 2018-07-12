---
layout: post
title: Learning Independent Causal Mechanisms
comments: True
excerpt: 
tags: ['2018', 'ICML 2018', 'Causal Learning', 'Causality', AI, ICML]
---

## Introduction

* The paper presents a very interesting approach for learning independent (inverse) data transformation from a set of transformed data points in an unsupervised manner.

* [Link to the paper](https://arxiv.org/abs/1712.00961)

## Formulation

* We start with a given data distribution *P* (say the MNIST dataset) where each x &epsilon; R<sup>d</sup>.

* Consider N transformations M<sub>1</sub>, ..., M<sub>N</sub> (functions that map input x to transformed input x'). Note that N need not be known before hand.

* These transformations can be thought of as independent (from other transformations) causal mechanisms.

* Applying these transformation would give N new distributions Q<sub>1</sub>, ..., Q<sub>N</sub>.

* These individual distributions are combined to form a single transformed distribution Q which contains the union of samples from the individual distributions.

* At training time, two datasets are created. One dataset corresponds to untransformed objects (sampled from *P*), referred to as *D<sub>P</sub>*. The other dataset corresponds to samples from the transformed distribution *Q* and is referred to as *D<sub>Q</sub>*.

* Note that all the samples in *D<sub>P</sub>* and *D<sub>Q</sub>* are sampled independently and no supervising information is needed.

* A series of N' parametric models, called as experts, are initialized and would be trained to learn the different mechanisms. 

* For simplicity, assume that N = N'. If N > N', some experts would learn more than one transformation or certain transformations would not be learnt. If N < N', some experts would not learn anything or some experts would learn the same distribution. All of these cases can be diagnosed and corrected by changing the number of experts.

* The experts are trained with the goal of maximizing an objective parameter *c*: R<sup>d</sup> to R. *c* takes high values on the support of  *P* and low values outside.

* During training, an example x<sub>Q</sub> (from D<sub>Q</sub>) is fed to all the experts at the same time. Each expert produces a value *c<sub>j</sub> = c(E<sub>j</sub>(x<sub>Q</sub>))*

* The winning expert is the one whose output is the max among all the outputs. Its parameters are updated to maximise its output while the other experts are not updated.

* This forces the best performing model to become even better and hence specialize.

* The objective *c* comes from adversarial training where a discriminator network discriminates between the untransformed input and the output of the experts.

* Each expert can be thought of as a GAN that conditions on the input x<sub>Q</sub> (and not on a noise vector). The output of the different experts is fed to the discriminator which provides both a selection mechanism and the gradients for training the experts.

## Experiments

* Experiments are performed on the MNIST dataset using the transformations like translation along 4 directions and along 4 diagonals, contrast shift and inversion.

* The discriminator is further trained against the output of all the losing experts thereby furthering strengthing the winning expert.

### Approximate Identity Initialization

  * The experts are initialized randomly and then pretrained to approximate the identity function by training with identical input-output pairs.
  
  * This ensures that the experts start from a similar level. 
  
  * In practice, it seems necessary for the success of the proposed approach.
  
### Observations

* During the initial phase, there is a heavy competition between the experts and eventually different winners emerge for different transformations.

* The approximate quality of reconstructed output was also evaluated using a downstream task.
    * 3 type of inputs were created:
        * Untransformed images
        * Transformed images
        * Transformed images a being processed by experts.
    * These inputs are fed to a pretrained MNISTN classifier.
    * The classifier performs poorly on the transformed images while the performance for images processed by experts quickly catches up with the performance on untransformed images.

* The experts E<sub>i</sub> generalize on the data points from a different dataset as well.
    * To test the generalisation capabilities of the expert, a sample of data from the omniglot dataset is transformed and fed to experts (which are trained only on MNIST). 
    * Each expert consistently applies the same transformation even though the inputs are outside the training domain. 
    * This suggests that the experts have generalized to different transformations irrespective of the underlying dataset.
    
## Comments

* The experiments are quite limited in terms of complexity of dataset and complexity of transformation but it provides evidence for a promising connection between deep learning and causality.

* Appendix mentions that in case there are too many experts, for most of the tasks, only one model specialises and the extra experts do not specialize at all. This is interesting as there is no explicit regularisation penalty which prevents the emergence of multiple experts per task.