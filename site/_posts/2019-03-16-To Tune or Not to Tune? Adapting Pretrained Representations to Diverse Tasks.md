---
layout: post
title: To Tune or Not to Tune? Adapting Pretrained Representations to Diverse Tasks
comments: True
excerpt: 
tags: ['2019', 'Empirical Advice', 'Multi Task', 'Natural Language Processing', 'Transfer Learning', AI, NLP, QA]
---

* [Link to the paper](https://arxiv.org/abs/1903.05987)

* The paper provides useful empirical advice for adapting pretrained language models for a given target task.

* Pre-trained models considered
    
    * ELMo

    * BERT

* Tasks considered

    * Named Entity Recognition (NER) - CoNLL 2003 dataset

    * Sentiment Analysis (SA) - Stanford Sentiment Treebank (SST-2) dataset

    * Natural Language Inference (NLI) - MultiNLI and Sentences Involving Compositional Knowledge (SICK-E) dataset

    * Paraphrase Detection (PD) - Microsoft Research Paraphrase Corpus (MRPC)

    * Semantic Textual Similarity (STS) - Semantic Textual Similarity Benchmark (STS-B) and SICK-R

    * The last 3 tasks (NLI, PD, STS) are defined for sentence pairs.

* Adaptation Strategies

    * Feature Extraction

        * The pretrained model is only used for extracting features and its weights are kept fixed. 

        * For both ELMo and BERT, the contextual representation of the words from all the layers are extracted.

        * A weighted combination of these layers is used as an input to the task-specific model.

        * Task-specific models

            * NER - BiLSTM with CRF layer

            * SA - bi-attentive classification network

            * NLI, PD, STS - [Enhanced Sequential Inference Model (ESIM)](https://arxiv.org/abs/1609.06038)

    * Fine-tuning

        * The pretrained model is finetuned on the target task.

        * Task-specific models for ELMO    

            * NER - CRF on top of LSTM states

            * SA - Max-pool over the language model states followed by a softmax layer

            * NLI, PD, STS - cross sentence bi-attention between the language model states followed by pooling and softmax layer.

        * Task-specific models for BERT

            * NER - Extract representation of the first-word piece of each token followed by the softmax layer

            * SA, NLI, PD, STS - standard BERT training

* Main observations

    * Feature extraction and fine-tuning have comparable performance in most cases unless the two tasks are highly similar(fine-tuning is better) or highly dissimilar (feature extraction is better).

    * For ELMo, feature extraction consistently outperforms fine-tuning for the sentence pair tasks (NLI, PD, STS). The reverse trend is observed for BERT with fine-tuning being better on sentence pair tasks.

    * Adding extra parameters is helpful for feature extraction but not fine-tuning.

    * ELMo fine-tuning requires careful tuning and other tricks like triangular learning rates, gradual unfreezing and discriminative fine-tuning.

    * For the tasks considered, there is no correlation observed between the distance of the source and target domains and adaptation performance.

    * Training a diagnostic classifier (on the intermediate representations) suggests that fine-tuning improves the performance of the classifier at all the intermediate layers (which is sort of expected).

    * In terms of mutual information estimates, fine-tuned representations have a much higher mutual information as compared to the feature extraction based representations.

    * Knowledge for single sentence tasks seems to be mostly concentrated in the last layers while for pair classification tasks, the knowledge seems gradually build un in the intermediate layers, all the way up to the last layer.