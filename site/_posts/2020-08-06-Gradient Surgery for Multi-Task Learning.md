---
layout: post
title: Gradient Surgery for Multi-Task Learning
comments: True
excerpt: 
tags: ['2019', 'Gradient Manipulation', 'Multi Task', AI]


---



* The paper hypothesizes that main optimization challenges in multi-task learning arise because of negative interference between different tasks' gradients.

* It hypothesizes that negative interference happens when:

	* The gradients are conflicting (i.e., have a negative cosine similarity).

	* The gradients coincide with high positive curvature.

	* The difference in gradient magnitude is quite large.


* The paper proses to work around this problem by performing "gradient surgery."

* If two gradients are conflicting, modify the gradients by projecting each onto the other's normal plane. 

* This modification is equivalent to removing the conflicting component of the gradient.

* This approach is referred to as *projecting conflicting gradients* (PCGrad).

* [Link to the paper](https://arxiv.org/abs/2001.06782)

* Theoretical Analysis
	
	* The paper proves the local conditions under which PCGrad improves multi-task gradient descent in the two-task setup.

	* The conditions are:

		* Angle between the task gradients is not too small.

		* Difference in the magnitude of the gradients is sufficiently large.

		* Curvature of the multi-task gradient is large.

		* Large enough learning rate.

* Experimental Setup

	* Multi-task supervised learning

		* MutliMNIST, Multi-task CIFAR100, NYUv2.

		* For Multi-task CIFAR-100, PCGrad is used with the shared parameters of the routing networks.

		* For NYUv2, PCGrad is combined with MTAN.

		* In all the cases, using PCGrad improves the performance.

	* Multi-task Reinforcement Learning

		* Meta-World Benchmark

		* PCGrad + SAC outperforms all other baselines.

		* In the context of SAC, the paper suggests learning temperature $\alpha$ on a per-task basis.

	* Goal-conditioned Reinforcement Learning

		* Goal-conditioned robotic pushing task with a Sawyer robot.

		* PCGrad + SAC outperforms vanilla SAC.