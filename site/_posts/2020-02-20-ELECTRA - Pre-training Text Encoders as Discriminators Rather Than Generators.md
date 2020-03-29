---
layout: post
title: ELECTRA - Pre-training Text Encoders as Discriminators Rather Than Generators 
comments: True
excerpt: 
tags: ['2019', 'ICLR 2020', 'Natural Language Processing', AI, Attention, Finetuning, ICLR, NLP, Pretraining, Transformer]


---

## Introduction

* Masked Language Modeling (MLM) is a common technique for pre-training language-based models. The idea is to "corrupt" some tokens in the input text (around 15%) by replacing them with the [MASK] token and then training the network to reconstruct (or predict) the corrupted tokens.

* Since the network learns from only about 15% of the tokens, the computational cost of training using MLM can be quite high.

* The paper proposes to use a "replaced token detection" task where some tokens in the input text are replaced by other plausible tokens. 

* For each token in the modified text, the network has to predict if the token has been replaced or not.

* The alternative token is generated using a small generator network.

* Unlike the previous MLM setup, the proposed task is defined for all the input tokens, thus utilizing the training data more efficiently.

* [Link to the paper](https://openreview.net/forum?id=r1xMH1BtvB)

## Approach

* The proposed approach is called ELECTRA (Efficiently Learning an Encoder that Classifies Token Replacements Accurately)

* Two neural networks - Generator (G) and Discriminator (D) are trained.

* Each network has a Transformer-based text encoder that maps a sequence of words into a sequence of vectors.

* Given an input sequence x (of length N), k indices are chosen for replacing the tokens.

* For each index, the generator produces a distribution over tokens. A token is sampled to replace in the original sequence. The resulting sequence is referred to as the corrupted sequence.

* Given the corrupted sequence, the Discriminator predicts which token comes from the data distribution and which comes from the generator.

* The generator is trained using the MLM setup, and the Discriminator is trained using the discriminative loss.

* After pre-training, only the Discriminator is finetuned on the downstream tasks.

## Experiments

* Datasets

    * GLUE Benchmark

    * Stanford QA dataset

* Architecture Choices

    * Sharing word embeddings between generator and Discriminator helps.

    * Tying all the encoder weights leads to marginal improvement but forces the generator and the Discriminator to be of the same size. Hence only embeddings are shared. 

    * Generator model is kept smaller than the discriminator model as a strong generator can make the training difficult for the Discriminator.

    * A two-stage training procedure was explored where only the generator is trained for n steps. Then the weights of the generator are used to initialize the Discriminator. The Discriminator is then trained for n steps while keeping the generator fixed.

    * This two-stage setup provides a nice curriculum for the Discriminator but does not outperform the joint training based setup.

    * An adversarial loss based setup is also explored but it does not work well probably because of the following reasons:

        * Adverserially trained generator is not as good as the MLM generator.

        * Adverserially trained generator produces a low entropy output distribution.

* Results

    * Both small and large ELECTRA models outperform baselines models like [BERT](https://arxiv.org/abs/1810.04805), [RoBERTa](https://arxiv.org/abs/1907.11692), [ELMo](https://arxiv.org/abs/1802.05365) and [GPT](https://www.cs.ubc.ca/~amuham01/LING530/papers/radford2018improving.pdf).

* Ablations

    * ELECTRA-15 is a variant of ELECTRA where the Discriminator is trained on only 15% of the tokens (similar to the MLM setup). This reduces performance significantly.

    * Replace MLM setup

        * Perform MLM training, but instead of using [MASK], use a toke sampled from the generator. 

        * This improves the performance marginally.

    * All-token MLM

        * In the MLM setup, replace the [MASK] token by the sampled tokens and train the MLM model to generate all the words.

        * In practice, the MLM model can either generate a word or copy the existing word.

        * This approach closes much of the gap between BERT and ELECTRA.

* Interestingly, ELECTRA outperforms All-token MLM BERT suggesting the ELECTRA may be benefitting from parameter efficiency since it does not have to learn a distribution over all the words.