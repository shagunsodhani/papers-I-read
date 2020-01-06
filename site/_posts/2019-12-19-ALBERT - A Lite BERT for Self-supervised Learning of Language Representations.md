---
layout: post
title: ALBERT - A Lite BERT for Self-supervised Learning of Language Representations
comments: True
excerpt: 
tags: ['2019', 'ICLR 2019', 'Natural Language Processing', 'Representation Learning', AI, Attention, ICLR, NLP, Transformer, SOTA]

---


## Introduction

* The paper proposes parameter-reduction techniques to lower the memory consumption (and improve training speed) of BERT.

* It also proposes to use a self-supervised loss (based on inter-sentence coherence) and argues that this loss is better than the NSP loss used by BERT.

* [Link to the paper](https://arxiv.org/abs/1909.11942)

## Architecture

* ALBERT architecture is similar to that of BERT with three major differences.

* Factorized Embedding Parameterization

    * In BERT and followup works, the embedding size was tied to the size of the context vector. 

    * Since context vector is expected to encoder the entire context, it needs to have a large dimensionality.

    * One consequence of this choice is that even the embedding layer (which encodes the representation for each token) has a large size. This increases the overall memory footprint of the model.

    * The paper proposed to factorize the embedding parameters into two smaller matrics.

    * The embedding layer learns a low dimensional representation of the tokens and this representation is projected into a high dimensional space.

* Cross-layer parameter sharing
    
    * ALBERT shares all the parameters across the layers.

* Inter-sentence coherence loss
    
    * BERT uses two losses - Masked Language Modeling loss (MLM) and Next Sentence Prediction (NSP).

    * In the NSP task, the model is provided a pair of sentences and it has to predict if the two sentences appear consecutively in the same document or not. Negative samples are created by sampling sentences from different documents.

    * The paper argues that NSP is not effective as a loss function as it merges topic prediction and coherence prediction into one task (as the two sentences come from different documents). The topic prediction is an easier task as compared to coherence prediction.

    * Hence the paper proposes to use the Sentence Order Prediction task where the model has to predict which of the two sentences comes first in a document. The negative samples are created by simply swapping the order in the positive samples. Hence both the sentences come from the same document and topic prediction alone can not be used to solve the task.

## Setup

* Different variants (in terms of size) of ALBERT and BERT models are compared (eg ALBERT, ALBERT-x, BERT-x, etc).

* In general, ALBERT models have many-times fewer parameters as compared to the BERT models.

* Datasets - BookCorpus, English Wikipedia.

## Observations

* ALBERT-xxlarge significantly outperforms the BERT-large model even though it has around 70% parameters as the BERT-large model.

* BERT-xlarge performs worse than BERT-base hinting that it is difficult to train such large models.

* ALBERT models also have better data throughput as compared to BERT models.

* For the ALBERT models, an embedding size of 128 performs the best.

* As the hidden dimension is increased, the model obtains better performance, but with diminishing returns.

* Very wide ALBERT models (say with a context size of 1024) do not benefit much from depth.

* Using additional training data boosts the performance for most of the downstream tasks.

* The paper empirically shows that using dropout could hurt the performance of the ALBERT models. This observation may not hold for BERT as it does not share parameters across layers and hence may need regularization via dropout.

* ALBERT also improves the state of the art performance on GLUE, SQuAD and RACE benchmarks, for both single-model and ensemble setup.


