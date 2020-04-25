---
layout: post
title: Supervised Contrastive Learning 
comments: True
excerpt: 
tags: ['2020', 'Contrastive Learning', AI, Contrastive, ImageNet]

---

## Introduction

* The paper builds on the prior work on self-supervised contrastive learning and extends it for the supervised learning case where many positive examples are available for each anchor.

* [Link to the paper](https://arxiv.org/abs/2004.11362)

## Approach

* The representation learning framework has the following components:

### Data Augmentation Module

* This module transforms the input example. The paper considers the following strategies:

    * Random crop, followed by resizing
    * [Auto Augment](https://arxiv.org/abs/1805.09501) - A method to search for data augmentation strategies.
    * [Rand Augment](https://arxiv.org/abs/1909.13719) - Randomly sampling a sequence of data augmentations, with repetition
    * SimAugment - Sequentially apply random color distortion and Gaussian blurring, followed by probabilistic sparse image wrap.

### Encoder Network
    
    * This module maps the input to a latent representation.

    * The same network is used to encode both the anchor and the sample.

    * The representation vector is normalized to lie on the unit hypersphere.

### Projection Network
    
    * This module maps the normalized representation to another representation, on which the contrastive loss is computed.

    * This network is only used for training the supervised contrastive loss.

### Loss function

    * The paper extends the standard contrastive loss formulation to handle multiple positive examples.

    * The main effect is that the modified loss accounts for all the same-class pairs (from within the sampled batch as well as the augmented batch).

    * The paper shows that the gradient (corresponding to the modified loss) causes the learning to focus more on hard examples. "Hard" cases are the ones where contrasting the anchor benefits the encoder more.

    * The proposed loss can also be seen as a generalization of the triplet loss.

## Experiments

* Dataset - ImageNet

* Models - ResNet50, ResNet200

* The network is "pretrained" using supervised contrastive loss. 

* After pre-training, the projection network is removed, and a linear classifier is added.

* This classifier is trained with the CE loss while the rest of the network is kept fixed.

## Results

* Using supervised contrastive loss improves over all the baseline models and data augmentation approaches.

* The resulting classifier is more robust to image corruptions, as shown by the mean Corruption Error (mCE) metric on the ImageNet-C dataset.

* The model is more stable to the choice oh hyperparameter values (like optimizers, data augmentation, and learning rates).

## Training Details

* Supervised Contrastive loss is trained for 700 epochs during pre-training.

* Each step is about 50% more expensive than performing CE.

* The dense classifier layer can be trained in as few as ten epochs.

* The temperature value is set to 0.07. Using a lower temperature is better than using a higher temperature.

