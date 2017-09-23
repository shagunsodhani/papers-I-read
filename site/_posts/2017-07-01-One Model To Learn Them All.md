---
layout: post
title: One Model To Learn Them All
comments: True
excerpt: The paper explores the possibility of such a unified deep learning model that can solve different tasks across multiple domains by training concurrently on them.
tags: ['2017', AI, Attention, CV, Multi Modal, Multi Model, NLP, Speech]
---

* The current trend in deep learning is to design, train and fine tune a separate model for each problem.

* Though multi-task models have been explored, they have been trained for problems from the same domain only and no competitive multi-task, multi-modal models have been proposed.

* The paper explores the possibility of such a unified deep learning model that can solve different tasks across multiple domains by training concurrently on them.

* [Link to the paper](https://arxiv.org/abs/1706.05137)

## Design Philosophy

* Small, modality-specific subnetworks (called modality nets) should be used to map input data to a joint representation space and back.
    
    * The joint representation is to be of variable size.

    * Different tasks from the same domain share the modality net.

* MultiModel networks should use computational blocks from different domains even if they are not specifically designed for the task at hand.
    
    * Eg the paper reports that attention and mixture-of-experts (MOE) layers slightly improve the performance on ImageNet even though they are not explicitly needed.


## Architecture

* MulitModel Network consists of few, small modality nets, an encoder, I/O mixer and an autoregressive decoder.

* Encoder and decoder use the following computational blocks:
    
    * **Convolutional Block**

        * ReLU activations on inputs followed by depthwise separable convolutions and layer normalization.

    * **Attention Block** 

        * Multihead, dot product based attention mechanism.

    * **Mixture-of-Experts (MoE) Block**

        * Consists of simple feed-forward networks (called experts) and a trainable gating network which selects a sparse combination of experts to process the inputs.

    * For further details, refer the [original paper](https://arxiv.org/abs/1706.05137).

* **Encoder** consists of 6 conv blocks with a MoE block in the middle.

* **I/O mixer** consists of an attention block and 2 conv blocks.

* **Decoder** consists of 4 blocks of convolution and attention with a MoE block in the middle.

* **Modality Nets**

    * **Language Data**

        * Input is the sequence of tokens ending in a termination token.

        * This sequence is mapped to correct dimensionality using a learned embedding.

        * For output, the network takes the decoded output and performs a learned linear mapping followed by Softmax.

    * **Image** and **Categorical Data**

        * Uses residual convolution blocks.

        * Similar to the exit flow for [Xception Network](https://arxiv.org/abs/1610.02357)

    * **Audio Data**

        * 1-d waveform over time or 2-d spectrogram operated upon by stack of 8 residual convolution blocks.

## Tasks

* WSJ speech corpus

* ImageNet dataset

* COCO image captioning dataset

* WSJ parsing dataset

* WMT English-German translation corpus

* German-English translation

* WMT English-French translation corpus

* German-French translation

## Experiments

* The experimental section is not very rigorous with many details skipped (would probably be added later).

* While MultiModel does not beat the state of the art models, it does outperform some recent models.

* Jointly trained model performs similar to single trained models on tasks with a lot of data and sometimes outperformed single trained models on tasks with less data (like parsing).

* Interestingly, jointly training the model for parsing task and Imagenet tasks improves the performance of parsing task even though the two tasks are seemingly unrelated.

* Another experiment was done to evaluate the effect of components (like MoE) on tasks (like Imagenet) which do not explicitly need them. It was observed that either the performance either went down or remained the same when MoE component was removed. This indicates that mixing different components does help to improve performance over multiple tasks.

* But this observation is not conclusive as a different combination of say the encoder (that does not use MoE) could achieve better performance than one that does. The paper does not explore possibilities like these.  