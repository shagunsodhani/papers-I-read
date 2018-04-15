---
layout: post
title: Unsupervised Learning by Predicting Noise
comments: True
excerpt: The paper presents an approach to learn features in an unsupervised setting by using a set of target representations called as Noise As Target (NAT) which acts as a kind of proxy supervising signal.
tags: ['2017', AI, CV, Embedding, Unsupervised]
---

## Introduction

* Convolutional Neural Networks are extremely good feature extractors in the sense that features extracted for one task (say image classification) can be easily transferred to another task (say image segmentation).

* Existing unsupervised approaches do not aim to learn discriminative features and supervised approaches for discriminative features do not scale well.

* The paper presents an approach to learn features in an unsupervised setting by using a set of target representations called as Noise As Target (NAT) which acts as a kind of proxy supervising signal.

* [Link to the paper](https://arxiv.org/abs/1704.05310)

## Approach

### Unsupervised Setting

* Given a collection of image X (x<sub>1</sub>, x<sub>2</sub>, ..., x<sub>n</sub>), we want to learn a parameterized mapping *f* such that *f(x<sub>i</sub>)* gives the features of image *x<sub>i</sub>*. We would jointly learn the target vectors *y<sub>i</sub>* (more on it later).

### Loss Function

* Squared L2 norm is used as the distance measure while making sure that final activations are unit normalized.

### Fixed Target Representation

* In the setting of the problem where we are learning both the features and the target representation, a trivial solution would be the one where all the input images map to the same target and are assigned the same representation. No discriminative features are learned in this case.

* To avoid such situations, a set of k predefined target representations are chosen and each image is mapped to one of these k representations (based on the features).

* There is an assumption that k > n so that each image is assigned a different target.

* One simple choice of target representation is the standard one-hot vector which implies that all the class (and by extension, the associated images) are orthogonal and equidistant from each other. But this is not a reasonable approximation as not all the image pairs are equally similar or dissimilar.

* Instead, the target vectors are uniformly sampled from a d-dimensional unit sphere, where d is the dimensionality of the feature representation. That is, the idea is to map the features to the manifold of the d-dimensional L2 sphere by using the K predefined representations as for the discrete approximation of the manifold.

* Since each data point (image) is mapped to a new point on the manifold, the algorithm is suited for online training as well.

### Optimisation

* For the training, the number of target K is reduced to the number of images n and an assignment matrix P is learned which ensures that the mapping between the image to target is 1-to-1.

* The resulting optimisation equation can be solved using the Hungarian Algorithm but at a high-cost O(n^3). An optimisation is to take a batch of b images and update the square matrix P<sub>B</sub> for dimension bXb (made of the images and their corresponding targets). This reduces the overall complexity of O(nb^2).

* Other optimisation techniques, that are common to supervised learning, like batch norm used in this setting as well.

### Implementation Detail

* Used AlexNet with NATs to train the unsupervised model.

* An MLP is trained on these features to learn the classifier.

* Standard preprocessing techniques like random cropping/flipping are used.

### Experimental Details

* Dataset
    
    * ImageNet for training the AlexNet architecture with the proposed approach.

    * Pascal VOC 2007 for transfer learning experiments.

* Baselines

    * Unsupervised approaches like autoencoder, GAN, BiGAN

    * Self-supervised

    * SOTA models using hand-made features SIFT with Fisher Vector.

## Observation

* Using squared loss instead of softmax does not deteriorate the performance too much.

* The authors compare the effect of using discrete vs continuous target representations for transfer learning. For the discrete representation, elements of the canonical basis of a k-dimensional space (k=1000, 10000, 100000) are used. Experiments demonstrate that d-dimensional continuous vectors perform much better than the discrete vectors.

* While training the unsupervised network, its features were extracted after every 20 iterations to evaluate the performance on transfer learning task. The test accuracy increases up to around 100 iterations then saturate.

* Comparing the visualization of the first convolutional layer filters (for AlexNet with and without supervision) shows that while unsupervised filters are less sharp, they maintain the edge and orientation information.

* The proposed unsupervised method outperforms all the unsupervised baselines and is competitive with respect to the supervised baseline. But it is still far behind the model using handcrafted features.

* For transfer learning, on Pascal VOC, the proposed approach beats the supervised baseline and works at par with the supervised approach.

## Notes

* The paper proposed a simple unsupervised framework for learning discriminative features without having to rely on proxy tasks like image generation and without having to make an assumption about the input domain.

* The key aspect of the proposed approach is that each image is assigned to a unique point in the d-dimensional manifold which means 2 images could be very close to each other on the manifold while being quite distinct in reality. It is interesting to see that such a simple strategy is able to give such good results. 