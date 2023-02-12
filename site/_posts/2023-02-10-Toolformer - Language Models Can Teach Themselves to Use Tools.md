---
layout: post
title: Toolformer - Language Models Can Teach Themselves to Use Tools
comments: True
excerpt:
tags:
  [
    "2023",
    "Large Language Model",
    "PreTrained Langauge Model",
    AI,
    GPT,
    LLM,
    OPT,
  ]
---

## Introduction

- The paper presents Toolformer, a language model that uses simple APIs to use external tools (calculator, QA system, search engine, translation system, and calendar).

- [Link to the paper](https://arxiv.org/abs/2302.04761)

## Approach

- Starting with a language model, M, the goal is to enable the language model to use tools by invoking API calls.

- An API call is denoted by the tuple $c = (api-name, api-input)$. It can be linearized as $e(c) = [api-name(api-input)]$ or as $e(c, r) = [api-name(api-input) -> r]$ where $r$ denotes the result of the API.

- The given dataset of plain text, $C$, is converted into a dataset $C*$ augmented with the API calls using a three-step process.

- In the first step, a position ($i$) and API call candidates (for the position $i$) are sampled.

  - Positions are sampled by (i) computing the probability that M assigns to starting an API call for each position and (ii) retaining the top-$k$ positions with a probability greater than a threshold value.

  - For each of the sampled positions (say $i$), API calls are sampled by concatenating a prompt to the tokens till index $i$ and sampling from the model M. Examples that do not generate the "end of the API" token (i.e.,"]") are discarded.

- In the second step, the API calls are executed to obtain response $r$ (text sequence).

  - API calls are filtered using the following criteria: if providing M with both the input and the output of the API makes it easier for M to predict the future token, compared to not using the API call at all or just using the input to the API, then the API call is helpful for M, and the example should be retained.

- In the last step, the remaining API calls are merged to obtain the augmented dataset $C*$ that is used for finetuning M.

- Note that $C*$ contains $C$, so M is finetuned on the original dataset and examples where a tool is helpful.

- During inference, the model is used for decoding in the usual way. Decoding is stopped when it produces the "->" token, and the corresponding API is used to generate the response. The decoding process (using the model) resumes with the API output appended to the decoded text.

## Tools

- There are two constraints on the tools: (i) their input and output should be expressible as text, and (ii) few demonstrations can be obtained from the tools. The second constraint means that the tool should be useable or accessible.

- The paper considered the following tools: a question-answering system, a Wikipedia search engine, a calculator, a calendar, and a machine translation system. Of these, only the calculator and calendar are non-neural network tools.

## Experiments

- Subset of CCNet is used as the language modeling dataset.

- GPT-J is used as the language model.

- For finetuning, the batch size is 128, the learning rate is 1e-5, and a linear warmup for the first 10% of training is used.

- Following models are compared:

  - GPT-J: Regular GPT-J model without any finetuning.

  - GPT-J + CC: GPT-J finetuned on $C$ without any API calls.

  - Toolformer, i.e. GPT-J finetuned on $C*$.

  - Toolformer with API calls disabled during training.

  - OPT 66B

  - GPT-3

- The models are evaluated in the prompted zero-shot setup, where models are instructed to solve a task without any in-context examples.

- One difference from the standard greedy decoding is that the API call is used whenever it is one of the top-10 most likely next tokens. This is done to increase the use of API calls.

- Evaluation Tasks

  - SQuAD, GoogleRE, and T-REx subsets of the LAMA benchmark where the model has to complete a short statement with a missing fact.

    - Since LAMA questions are based on Wikipedia, Toolformer isn't allowed to use Wikipedia search.

    - The evaluation criteria is to check if the correct word is among the first five words predicted by the model.

    - Toolformer uses the question-answering tool for most cases, outperforming all the baselines.

  - Math Dataset

    - eSDiv, SVAMP, and MAWPS benchmarks.

    - The first number predicted by the model is considered to be the output.

    - Toolformer uses the calculator tool for most cases, thereby outperforming all the baselines.

  - Question Answering

    - Web Questions, Natural Questions, and TriviaQA datasets.

    - The evaluation criteria is to check if the correct word is among the first 20 words predicted by the model.

    - Question Answering tool is disabled for this setup.

    - Toolformer uses the Wikipedia tool for most cases, thereby outperforming all the baselines other than the much larger GPT-3 model.

  - Multilingual Question Answering

    - MLQA benchmark.

    - The evaluation criteria is to check if the correct word is among the first ten words predicted by the model.

    - Toolformer uses the translation tool for most of the questions, with questions in Hindi being an exception.

    - However, Toolformer does not consistently outperform the GPT-J baseline, likely because, for some languages, finetuning on CCNet could hurt performance.

  - Temporal Datasets

    - TEMPLAMA (cloze style queries where the answer changes with time) and DATESET (dataset generated through a series of templates and populated with random dates/durations).

    - While Toolformer outperforms the baselines for both datasets, it relies on the Wikipedia search and Question Answering tools (and not the calendar tool) for the LAMA dataset. On the DATESET dataset, it uses the calendar tool in the majority.

  - Language Modeling

    - WikiText and a subset of 10,000 randomly selected documents from CCNet (not used during training of M).

    - Training on $C*$ does not increase perplexity (compared to training on C). In this experiment, the API calls are disabled during inference.

- Varying the size of the underlying models show that the ability to use tools emerges only around 755M parameters.

## Future Work

- Extending Toolformer to chain the use of tools and use tools interactively.

- In some cases, the use of tools is very sample-inefficient.

- Decision to use a tool does not account for the cost of using the tool.
