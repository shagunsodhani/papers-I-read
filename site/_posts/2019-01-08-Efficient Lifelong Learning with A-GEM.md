---
layout: post
title: Efficient Lifelong Learning with A-GEM
comments: True
excerpt: 
tags: ['2019', 'Catastrophic Forgetting', 'Continual Learning', 'ICLR 2019', 'Lifelong Learning', AI, CV, CL, ICLR]
---

## Contributions

* A new (and more realistic) evaluation protocol for lifelong learning where each data point is observed just once and a disjoint set of tasks are used for training and validation.

* A new metric that focuses on the efficiency of the models - in terms of sample complexity and computational (and memory) costs.

* Modification of [Gradient Episodic Memory ie GEM](https://arxiv.org/abs/1706.08840) which reduces the computational overhead of GEM without compromising on the results.

* Empirical validation that using task descriptors help lifelong learning models and improve their few-shot learning capabilities.

* [Link to the paper](https://arxiv.org/abs/1812.00420)

* [Link to the code](https://github.com/facebookresearch/agem/)

## Learning Protocol

* Two group of datasets - one for training and evaluation (D<sup>EV</sup>) and other for cross validation (D<sup>CV</sup>).

* Data can be sampled multiple times for cross-validation dataset but only once from the training dataset.

* Each group of dataset (say D<sup>EV</sup> or D<sup>CV</sup>) is a list of task-specific datasets D<sub>k</sub> (k is the task index).

* Each sample in D<sub>k</sub> is of the form (x, t, y) where x is the data, t is the task descriptor and y is the output.

* D<sub>k</sub> contains B<sup>k</sup> minibatches of data.

## Metrics

### Accuracy

* a<sub>k,i,j</sub> = accuracy on test task j after training on ith minibatch of training task k.

* A<sub>k</sub> = mean over all j = 1 to k (a<sub>k, B<sub>k</sub>, j</sub>) ie train the model on data for task k and then test it on all the tasks.

### Forgetting Measure

* f<sub>j</sub><sup>k</sup> = forgetting on task j after training on all minibatches upto task k.

* f<sub>j</sub><sup>k</sup> = max over all l = 1 to k-1 (a<sub>l, B<sub>l</sub>j</sub> - a<sub>k, B<sub>k</sub>j</sub>)

* Forgetting = F<sub>k</sub> = mean over all j = 1 to k-1 (f<sub>j</sub><sup>k</sup>)

### LCA - Learning Curve Area

* Z<sub>b</sub> = average b shot performance where b is the minibatch number.

* Z<sub>b</sub> = mean over all k = 0 to T (a<sub>k, b, k</sub>)

* LCA<sub>&beta;</sub> = mean over all b = 0 to &beta; (Z<sub>b</sub>)

* One special case is LCA<sub>0</sub> which is the forward transfer performance or performance on the unseen task.

* In experiments, &beta; is kept small as we want the model to learn from few examples.

## Model

* GEM has been shown to be very effective in single epoch setting but introduces a very high computational overhead.

* Average GEM (AGEM) reduces this overhead by sampling (and using) only some examples from the episodic memory instead of using all the examples.

* While GEM provides better guarantees in terms of worst-case forgetting, AGEM provides better guarantees in terms of average accuracy.

## Joint Embedding Model Using Compositional Task Descriptors

* Compositional Task Descriptors are used to speed training on the subsequent tasks.

* A matrix specifying the attribute value of objects (to be recognized in the task) are used.

* A joint-embedding space between image features and attribute embeddings is learned.

## Experiments

### Datasets

* [Permuted MNIST](https://arxiv.org/abs/1612.00796)

* [Split CIFAR](https://arxiv.org/abs/1703.04200)

* [Split CUB](http://www.vision.caltech.edu/visipedia/CUB-200-2011.html)

* [Split AWA](http://cvml.ist.ac.at/papers/lampert-cvpr2009.pdf)

### Setup

* Integer task descriptors for MNIST and CIFAR and class attributes as descriptors for CUB and AWA

* Baselines include [GEM](https://arxiv.org/abs/1706.08840), [iCaRL](https://arxiv.org/abs/1611.07725), [Elastic Weight Consolidation](https://arxiv.org/pdf/1612.00796.pdf), [Progressive Neural Networks](https://arxiv.org/abs/1606.04671) etc.

## Results

* AGEM outperforms other models on all the datasets expect MNIST where the Progressive Neural Networks lead. One reason could be that MNIST has a large number of training examples per task. But Progressive Neural Networks lead to bad utilization of capacity.

* While AGEM and GEM have similar performance, GEM has a much higher computational and memory overhead.

* Use of task descriptors improves the accuracy for all the models.

* It seems that AGEM offers a good tradeoff between average accuracy performance and efficiency - in terms of sample efficiency, memory requirements and computational costs.
