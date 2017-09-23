---
layout: post
title: Conditional Similarity Networks
comments: True
excerpt: A common way of measuring image similarity is to embed them into feature spaces where distance acts as a proxy for similarity.
tags: [CV, AI, CVPR, Embedding, 'CVPR 2017', '2017']
---

## Problem Statement

* A common way of measuring image similarity is to embed them into feature spaces where distance acts as a proxy for similarity.
* But this feature space can capture one (or a weighted combination) of the many possible notions of similarity.
* What if contracting notions of similarity could be captured at the same time - in terms of semantically distinct subspaces.
* The paper proposes a new architecture called as Conditional Similarity Networks (CSNs) which learns a disentangled embedding such that the features, for different notions of similarity, are encoded into separate dimensions.
* It jointly learns masks (or feature extractors) that select and reweights relevant dimensions to induce a subspace that encodes a specific notion of similarity.
* [Link to the paper](https://vision.cornell.edu/se3/conditional-similarity-networks/)

## Conditional Similarity Networks

* Given an image, *x*, learn a non-linear feature embedding *f(x)* such that for any 2 images *x<sub>1</sub>* and *x<sub>2</sub>*, the euclidean distance between *f(x<sub>1</sub>)* and *f(x<sub>2</sub>)* reflects their similarity.

### Conditional Similarity Triplets

* Given a triplet of images *(x<sub>1</sub>, x<sub>2</sub>, x<sub>3</sub>)* and a condition *c* (the notion of similarity), an oracle (say crowd) is used to determmine if *x<sub>1</sub>* is more similar to *x<sub>2</sub>* or *x<sub>3</sub>* as per the given criteria *c*.
* In general, for images *i, j, l*, the triplet *t* is ordered {i, j, l \| c} if *i* is more similar to *j* than *l*.

### Learning From Triplets

* Define a loss function *L<sub>T</sub>()* to model the similarity structure over the triplets.
* *L<sub>T</sub>(i, j, l) = max{0, D(i, j) - D(i, l) + h}* where *D* is the euclidean distance function and *h* is the similarity scalar margin to prevent trivial solutions.
* To model conditional similarities, masks *m* are defined as *m = σ(β)* where σ is the RELU unit and β is a set of parameters to be learnt.
* *m<sub>c</sub>* denotes the selection of the c-th mask column from feature vector. It thus acts as an element-wise gating function which selects the relevant dimensions of the embedding to attend to a particular similarity concept.
* The euclidean function *D* now computes the masked distance (*f(i, c)m<sub>c</sub>*) between the two given images.
* Two regularising terms are also added - L2 norm for *D* and L1 norm for *m*.
 
## Experiments

### Datasets

* Fonts dataset by Bernhardsson
    * 3.1 million 64 by 64-pixel grey scale images.
* Zappos50k shoe dataset
    * Contains 50,000 images of individual richly annotated shoes.
    * Characteristics of interest:
        * Type of the shoes (i.e., shoes, boots, sandals or slippers)
        * Suggested gender of the shoes (i.e., for women, men, girls or boys)
        * Height of the shoes’ heels (0 to 5 inches)
        * Closing mechanism of the shoes (buckle, pull on, slip on, hook and loop or laced up)

### Models

* Initial model for the experiments is a ConvNet pre-trained on ImageNet
* **Standard Triplet Network**
    * Learn from all available triplets jointly as if they have the same notion of similarity.
* **Set of Task Specific Triplet Networks**
    * Train n separate triplet networks such that each is trained on a single notion of similarity.
    * Needs far more parameters and compute.
* **Conditional Similarity Networks - fixed disjoint masks**
    * In this version, only the convolutional filters and the embedding is learnt and masks are predefined to be disjoint.
    * Aims to learn a fully disjoint embedding.
* **Conditional Similarity Networks - learned masks**
    * Learns all the components - conv filters, embedding and the masks.
* Refer paper for details on hyperparameters.

## Results

* Visual exploration of the learned subspaces (t-sne visualisation) show that network successfully disentangles different features in the embedded vector space.
* The learned masks are very sparse and share dimensions. This shows that CSNs may learn to only use the required number of dimensions thereby doing away with the need of picking the right size of embedding.
* Order of performance:
    * CSNs with learned masks > CSNs with fixed masks > Task-specific networks > standard triplet network.
    * Though CSNs with learned masks require more training data.
* CSNs also outperform Standard Triplet Network when used as off the shelf features for (brand) classification task and is very close to the performance of ResNet trained on ImageNet.
* This shows that while CSN retained most of the information in the original network, the training mechanism of Standard Triplet Network hurts the underlying conv features and their generalising capability
