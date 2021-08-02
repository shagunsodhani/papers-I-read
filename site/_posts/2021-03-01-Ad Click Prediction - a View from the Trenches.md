---
layout: post
title: Ad Click Prediction - a View from the Trenches
comments: True
excerpt: 
tags: ['2013', 'Click-Through Rate', 'Data Mining', 'Empirical Advice', 'KDD 2013', 'Machine Learning', Ads, CTR, Engineering, KDD, ML, Scale, Systems]

---

## Introduction

* The paper presents case studies from the experience of deploying an ad click-through rate (CTR) prediction model at Google.

* The paper focuses on themes related to memory footprint, performance analysis, calibration, confidence in the predictions, and feature engineering.

* [Link to the paper](https://research.google/pubs/pub41159/)

## System Overview

* Features (corresponding to a given ad) include search query and the metadata in the ad. The features are very sparse.

* Single layer, regularized Logistic Regression model is trained with Online Gradient Descent (same as Stochastic Gradient Descent, but in the online setting).

* From a memory perspective, it is important to minimize the size of the final model.

* Adding just the L1 penalty is not sufficient to produce weights that are precisely equal to 0.

* ["Follow The (Proximally) Regularized Leader" algorithm or FTRL-Proximal algorithm](http://proceedings.mlr.press/v15/mcmahan11b.html) is used to learn sparse models without losing on the accuracy.

* Using per-coordinate learning rates improves the performance at the cost of memory as both the sum of gradients and the sum of the square of gradients are tracked for each feature.

	* In practice, some of the cost can be alleviated by approximating that all the events containing a given feature have the same probability.

	* In such a case, the sum of the square of gradients can be approximated using the counts of positive and negative events alone.

* Some memory overhead can be reduced based on the following observation: the vast majority of features are extremely rare. Hence, it is not necessary to track the statistics for such rare features.

	* However, in an online setting, it is not known upfront as to which features will be sparse. 

	* The paper proposes to use probabilistic feature inclusion - a feature is added to the model with probability $p$. Once it is added, the feature is not removed.

	* An alternative approach is to use a rolling set of counting Bloom filters to check if a feature has appeared at least $n$ times in training. Bloom filters are probabilistic data structures and can return false positives.

* Memory can also be saved by using fewer bits for encoding weights.

	* Most of the weight coefficients lie in the range $(-2, 2)$, and a $16-$ bit encoding is used in place of $32$ or $64$ bit encoding.

	* This quantization approach needs to account for roundoff problems. The fix is easy to implement.

* When training many models with similar hyperparameters, per-model learning rate counters can be replaced by statistics shared by all the models, thus reducing memory footprint.

* A Single Value Structure is used to reduce the memory footprint when evaluating a very large set of model variants that differ only in addition/removal of a small subset of features.
	
	* All the models, that use a feature, share a single value structure corresponding to the feature. This reduces the memory overhead by order of magnitude.

	* During the update, each model computes the weight updates corresponding to all the features that it is using. The updated weight is averaged across all the models and used to update the single value structure.

* Since CTR datasets are generally highly imbalanced, the training data (for the negative class) can be subsampled to reduce the amount of data to train over. The loss component (corresponding to negative class) can be appropriately scaled up.

* Metrics
	
	* Offline metrics like AucLoss (1 - AUC), Log Loss, Squared Error

	* Online loss is computed on the new training data (new incoming traffic)	before training on it.

* The confidence in the model's prediction is estimated using a heuristic called *uncertainty score*. It can be measured using the dot product of the feature and the vector of learning rates.
	
	* The idea is that the learning rates already maintain a notion of uncertainty.

	* Features for which the learning rate is high are the features for which uncertainty is also high.

* Calibrating Predictions

	* The calibration can be improved by applying correction functions $\tau_d(p)$ where $p$ is the predicted CTR, and $d$ is an element of a partition of the training data.

	* $\tau$ can be modeled as $\gamma^{\kappa}$ where $\gamma$ and $\kappa$ are learned using Poisson regression.

* Unsuccessful Experiments

	* Aggressive feature hashing was tried to reduce the memory overhead. However, it leads to a significant loss in performance.

	* Using dropout did not help, probably because the features are sparse.

	* Using feature bagging hurt the AucLoss.

	* Feature vector normalization did not improve performance, probably because of per-coordinate learning rates and regularization.
