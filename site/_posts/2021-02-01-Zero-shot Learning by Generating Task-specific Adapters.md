---
layout: post
title: Zero-shot Learning by Generating Task-specific Adapters
comments: True
excerpt: 
tags: ['2021', 'Natural Language Processing', 'Text-to-Text Transformer', 'Zero-Shot', 'Zero Shot Generalization', Adapter, AI, HyperNetwork, NLP, Transformer]

---

## Introduction

* The paper introduces HYPTER - a framework for zero-shot learning (ZSL) in text-to-text transformer models by training a [**Hyp**erNetwork](https://shagunsodhani.com/papers-I-read/HyperNetworks) to generate task-specific [adap**ter**s](https://arxiv.org/abs/1902.00751) from task descriptions.

* The focus is on *in-task* zero-shot learning (e.g., learning to predict an unseen class or relation) and not on *cross-task* learning (e.g., training on sentiment analysis and evaluating on question-answering task).

* [Link to the paper](https://arxiv.org/abs/2101.00420)


## Terminology

* *Task* - a NLP task, like classification or question answering.

* *Sub-task*
    
    * A class/relation/question within a task.

    * Denotes by a tuple $(d, D)$ where $d$ is the language description while $D$ represents the subtask's dataset.

## Setup

* Develop ZSL approach for transfer to new subtasks within a task, using the task description available for each subtask.

## Approach

* HYPTER has two main parts:

    * Main network
        
        * A pretrained text-to-text network
        
        * Instantiated as a BERT-Base/Large

    * HyperNetwork
        
        * Generates the weights for adapter networks that will be plugged into the main network.
        

* HyperNetwork has two parts:

    * Encoder
        
        * Encodes the task description
        
        * Instantiated as a RoBERTa-Base model

    * Decoder
        
        * Decodes the encoding into weights for multiple adapters (in parallel)
        
        * Instantiated as a Feedforward Network
    
* The model trains in two phases:

    * Main network is trained on all the data by concatenating the task description with the input.

    * Adapters are trained by sampling a task from the train set while keeping the main network frozen.

## Experiments

* While the idea is very promising and interesting, the evaluation felt quite limited. It uses just two datasets [Zero-shot learning from Task Descriptions](https://leaderboard.allenai.org/zest/submissions/public) and [Zero-shot Relation Extraction](https://eval.ai/web/challenges/challenge-page/689/overview) and shows some improvements over the baseline of directly finetuning with task descriptions as the prompt.