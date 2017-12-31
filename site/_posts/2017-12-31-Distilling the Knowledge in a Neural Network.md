---
layout: post
title: Distilling the Knowledge in a Neural Network
comments: True
excerpt: 
tags: ['2014', 'NIPS 2014', AI, NIPS]
---

# Introduction

* In machine learning, it is common to train a single large model (with a large number of parameters) or ensemble of multiple smaller models using the same dataset.

* While such large models help to improve the performance of the system, they also make it difficult and computationally expensive to deploy the system.

* The paper proposes to transfer the knowledge from such "cumbersome" models into a single, "simpler" model which is more suitable for deployment. This transfer of knowledge is referred to as "distillation".

* [Link to the paper](https://arxiv.org/abs/1503.02531)

# Idea

* Train the cumbersome model using the given training data in the usual way.

* Train the simpler, distilled model using the class probabilities (from the cumbersome model) as the soft target. Thus, the simpler model is trained to generalise the same way as the cumbersome model.

* If the soft targets have high entropy, they provide much more information than the hard targets and the gradient (between training examples) would vary lesser.

* One approach is to minimise the L2 difference between logits produced by the cumbersome model and the simpler model. This approach was pursued by [Buciluǎ et al.](https://www.cs.cornell.edu/~caruana/compression.kdd06.pdf)

* The paper proposes a more general solution which they name "distillation". The temperature of the final softmax is increased till the cumbersome model produces a set of soft targets (from the final softmax layer). These soft targets are then used to train the simpler model.

* It also shows that the proposed approach is, in fact, a more general case of the first approach.

# Approach

* In the simplest setting, the cumbersome model is first trained with a high value of temperature and then the same temperature value is used to train the simpler model. The temperature is set to 1 when making predictions using the simpler model.

* It helps to add an auxiliary objective function which corresponds to the cross-entropy loss with the correct labels. The second objective function should be given a much lower weight though. Further, the magnitude of the soft targets needs to be scaled by multiplying with the square of temperature.

# Experiment

* The paper reports favourable results for distillation task for the following domains:

    * Image Classification (on MNIST dataset)

        * An extra experiment is performed where the simpler model is not shown any images of "3" but the model fails for only 133 cases out of 1010 cases involving "3".

    * Automatic Speech Recognition (ASR)

        * An extra experiment is performed where the baseline model is trained using both hard targets and soft targets alternatively. Further, only 3% of the total dataset is used. 

        * The model using hard targets overfits and has poor test accuracy while the model using soft targets does not overfit and gets much better test accuracy. This shows the regularizing effect of soft targets.

    * Training ensemble specialists for very large datasets (JFT dataset - an internal dataset at Google)

        * The experiment shows that while training a single large model would take a lot of time, the performance of the model can be improved by learning a small number of specialised networks (which are faster to train).

        * Though it is yet to be shown that the knowledge of such specialist models can be distilled back into a single model.