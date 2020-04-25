---
layout: post
title: CURL - Contrastive Unsupervised Representations for Reinforcement Learning 
comments: True
excerpt: 
tags: ['2020', 'Contrastive Learning', 'Deep Reinforcement Learning', 'Reinforcement Learning', 'Self Supervised', 'Sample Efficient', AI, Contrastive, DRL, RL, Unsupervised]

---

## Introduction

* The paper proposes a contrastive learning approach, called CURL, for performing off-policy control from raw pixel observations (by transforming them into high dimensional features). 

* The idea is motivated by the application of contrastive losses in computer vision. But there are additional challenges:

    * The learning agent has to perform both unsupervised and reinforcement learning.

    * The "dataset" for unsupervised learning is not fixed and keeps changing with the policy of the agent.

* Unlike prior work, CURL introduces fewer changes in the underlying RL pipeline and provides more significant sample efficiency gains. For example, CURL (trained on pixels) nearly matches the performance of SAC policy (trained on state-based features).

* [Link to the paper](https://github.com/MishaLaskin/curl)

## Implementation

* CURL uses instance discrimination. Deep RL algorithms commonly use a stack of temporally consecutive frames as input to the policy. In such cases, instance discrimination is applied to all the images in the stack.

* For generating the positive and negative samples, random crop data augmentation is used.

* Bilinear inner product is used as the similarity metric as it outperforms the commonly used normalized dot product.

* For encoding the anchors and the samples, InfoNCE is used. It learns two encoders $f_q$ and $f_k$ that transform the query (base input) and the key (positive/negative samples) into latent representations. The similarity loss is applied to these latents.

* Momentum contrast is used to update the parameters ($\theta_k$) of the $f_k$ network. ie $\theta_k = m \theta_k + (1-m) \theta_q$. $\theta_q$ are the parameters of the $f_q$ network and are updated in the usual way, using both the contrastive loss and the RL loss.

## Experiment

* DMControl100K and Atart100K refer to the setups where the agent is trained for 100K steps on DMControl and Atari, respectively.

* Metrics:
    
    * Sample Efficiency - How many steps does the baseline need to match CURL's performance after 100K steps.

    * Performance - Ratio of episodic returns by CURL vs. the baseline after 100K steps.

* Baselines:
    
    * DMControl

        * [SAC-AE](https://arxiv.org/abs/1910.01741)
        * [SLAC](https://arxiv.org/abs/1907.00953)
        * [PlaNet](https://planetrl.github.io/)
        * [Dreamer](https://openreview.net/forum?id=S1lOTC4tDS)
        * [Pixel SAC](https://arxiv.org/abs/1812.05905)
        * SAC trained on state-space observations

    * Atari

        * [SimPLe](https://arxiv.org/abs/1903.00374)
        * [RainbowDQN](https://arxiv.org/abs/1710.02298)
        * [OTRainbow (Over Trained Rainbow)](https://openreview.net/forum?id=Bke9u1HFwB)
        * [Efficient Rainbow](https://arxiv.org/abs/1906.05243)
        * Random Agent
        * Human Performance

* Results
    
    * DM Control

        * CURL outperforms all pixel-based RL algorithms by a significant margin for all environments on DMControl and most environments on Atari.

        * On DMControl, it closely matches the performance of the SAC agent trained on state-space observations.

        * On Atari, it achieves better median human normalizes score (HNS) than the other baselines and close to human efficiency in three environments.