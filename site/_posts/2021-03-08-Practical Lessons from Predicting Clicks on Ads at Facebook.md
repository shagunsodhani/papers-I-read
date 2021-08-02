---
layout: post
title: Practical Lessons from Predicting Clicks on Ads at Facebook
comments: True
excerpt: 
tags: ['2014', 'Click-Through Rate', 'Data Mining', 'Empirical Advice', 'KDD 2014', 'Machine Learning', Ads, CTR, Engineering, KDD, ML, Scale, Systems]

---

## Introduction

* The paper describes several design choices for developing a model for predicting user response (clicks) on ads.

* [Link to the paper](https://research.fb.com/publications/practical-lessons-from-predicting-clicks-on-ads-at-facebook/)

## Experimental Setup

* The model is trained/evaluated on offline data.

* Evaluation metrics:

	* Normalized Cross-Entropy (or Normalized Entropy, NE)

		* Defined as the predictive log-loss per impression, divided by the entropy of the background CTR (click-through rate).

		* Background CTR is the average empirical CTR of the training data.

		* Lower normalized cross-entropy is better.

		* The normalization term is important to make the metric insensitive to the background CTR. Otherwise, the log loss can easily be made low when background CTR is close to 0 or 1.

		* NE can also be written as $RIG - 1$, where $RIG$ is the Relative Information Gain.

	*  Calibration

		* Ratio of average estimated CTR and empirical CTR.

	* Area-Under-ROC (AUC) is a good metric for measuring ranking quality (among ads). However, it is **not used** as a metric to avoid over-delivery or under-delivery of ads.

## Implementation Details

* Feature Transformation

	* A given add impression, $e$, is transformed into a $n-$dimensional vector, $x$, where the $i^{th}$ index denotes the value of the $i^{th}$ categorical feature. 

	* Continous features are binned, and the bin index is used as a categorical feature, thus applying a non-linear transformation to the features.

	* Categorical features that are tuple-like (i.e., have a tuple of values) can be converted into new categorical features by taking a cartesian product.

	* Boosted decision trees can be used to implement the previous two transformations in one go. 

		* Each tree is used as a categorical feature that takes the value of the index of the leaf node than an ad maps to. 

		* The paper used the Gradient Boosting Machine with the $L_2-$TreeBoost algorithm.

		* Using the tree feature transformation improves the Normalized Cross-Entropy by $3.4\%$.

* Model
	
	* Logistic Regression (LR) or Bayesian online learning scheme for probit regression (BOPR) algorithms are used for training a linear classifier model.

	* While both LR and BOPR models provide similar performance, the LR model is half the BOPR model's size and faster for performing training/inference.

## Role of Data Freshness

* When a model is trained on the data from a particular day and evaluated on data from the subsequent days, the model's performance degrades as the delay between training and test set increases.

* This highlights the importance of the freshness of the training data. 

* One straightforward approach can be to train the model every day.

* Alternatively, the linear classifier can be trained using online learning, while the boosted decision tree can still be trained daily.

* Different choices for setting the learning rate (for online training of linear classifier) are compared, and the [per-coordinate learning rate](https://research.google/pubs/pub41159/) is found to perform best in practice.

## Generating Real-Time Training Data

* An "online joiner" system is used to generate real-time training data for the linear classifier.

* The challenging part is, while there are data points with a "positive" label (i.e., the user clicked on the ad), there are no datapoints with a "negative" label (since there is no "no-click" button that the user can click).

* An impression is considered to have the "no-click" label if the user does not click on the ad within a (long) time window of seeing the ad.

* Too short a time window could mislabel some impressions, while too long a time window will delay the real-time training data.

* The online joiner performs a distributed stream-to-stream join on the stream of ad impressions and stream of ad clicks using a HashQueue.

* A HashQueue:

	* comprises of a First-In-First-Out queue as a buffer window and a hash map for fast random access to label impressions.

	* supports three operations on key-value pairs: enqueue, dequeue, and lookup.

## Memory and Latency

* Increasing the number of boosting trees shows diminishing returns, and most of the improvements come from the first 500 trees.

* Top 10 features account for half of the total feature importance, while the last 300 features add less than 1% feature importance.

* Features in the boosting model can be broadly classified as contextual or historical.

* Historical feature provides much more explanatory power than the contextual features through contextual features are helpful to handle the cold start problem.

* Models trained with just the contextual features rely more heavily on data freshness than models trained with just the historical features.

* Uniform subsampling and negative downsampling techniques are used to limit the amount of training data.

* In the case of negative downsampling, the model needs to be re-calibrated as well.