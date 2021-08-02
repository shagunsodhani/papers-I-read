---
layout: post
title: Deep Neural Networks for YouTube Recommendations
comments: True
excerpt: 
tags: ['2016', 'Machine Learning', 'Recommender Systems', ACM, Engineering, Latency, ML, Ranking, Recommender, Scale, Systems]

---

## Introduction

* The paper describes YouTube's deep learning-based recommendation system.

* [Link to the paper](https://research.google/pubs/pub45530/)

## Challenges

* Scale - Very large number of users and videos.

* Freshness - Very large number of videos uploaded every hour. The recommendation system should take these new videos into account as well.

* Noise - User satisfaction needs to be modeled from noisy implicit feedback signal as the explicit signal is very sparse.

## System Overview

* Two neural networks: one for candidate generation and another one for ranking.

* Metrics

	* Offline metrics like precision, recall, ranking loss

	* A/B testing via live experiments

### Candidate Generation

* Input: events from a user's YouTube activity history.

* Output: small subset (hundreds) of videos.

* Approach: 

	* Recommendation is modeled as extreme multiclass classification.

	* Predict the video (from a corpus) that a user will watch at a given time.

	* The neural network's task is to learn useful user embeddings, given the user's context and history.

	* For each positive class (relevant video), negative classes (non-relevant videos) are sampled from the video corpus.

* Model Architecture

	* A feedforward network with input as user embeddings and context embeddings (watch history).

	* Watch history is a variable-length sequence of video ids, where each video id is mapped to an embedding.

	* The sequence of video ids is mapped to a sequence of embeddings, and this sequence is averaged to obtain fixed-sized embedding.

	* Additional signals like demographic features and search query embeddings can be added along with the context embeddings.

	* The age of a video is also used as a feature during training to account for the freshness of the content. This feature is set to zero (or slightly negative) during inference.

* Other Insights

	* Training examples are generated from all YouTube watches, including the watches from the videos embedded on other sites, to surface new content.

	* Generating the same number of training examples per user is important to avoid a small set of active users from dominating the model training.

	* Predicting a user's next watch leads to better results than predicting a randomly held-out watch. This can be attributed to the general consumption pattern of videos (e.g., episodes are usually watched in order).

### Ranking 

* Input: list of candidate videos to rank from.

* Output: score for each video.

* Approach

	* A feedforward network (similar to candidate generation model) trained using logistic regression loss.

* Feature representation

	* Different types of features: categorical vs. continuous, univalent vs. multivalent, describes video vs. describes user or context.

	* Important signals include user's interaction with the video (or similar videos), which source/channel added the video to the candidate set.

	* Embeddings are shared across features. For example, the representation for a video id remains the same, irrespective of whether it is being used for representing the "video to recommend" or the "last seen video."

	* Feature normalization and transformations like exponents (square or square root) for continuous variables improve the performance.

* To model the expected watch time, the logistic regression loss is weighted by the observed watch time. For example, if a video was watched, its weight is given by the observed watch time, and if the video was not watched, its weight is set to 1.

* In practice, this means that the logistic regression model learns odds that approximate the expected watch time of the video.