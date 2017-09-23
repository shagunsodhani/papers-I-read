---
layout: post
title: ReasoNet - Learning to Stop Reading in Machine Comprehension
comments: True
excerpt: Reasoning Network (ReasoNet) uses reinforcement learning (RL) to decide how many times a document should be read.
tags: ['2017', 'KDD 2017', AI, KDD, Machine Comprehension, NLP, QA, RL, SOTA]
---

## Introduction

* In the domain of machine comprehension, making multiple passes over the given document is an effective technique to extract the relation between the given passage, question and answer.

* Unlike previous approaches, which perform a fixed number of passes over the passage, Reasoning Network (ReasoNet) uses reinforcement learning (RL) to decide how many times a document should be read.

* Every time the document is read, ReasoNet determines whether the document should be read again or has the termination state been reached. If termination state is reached, the answer module is triggered to generate the answer.

* Since the termination state is discrete and not connected to the final output, RL approach is used.

* [Link to the paper](https://arxiv.org/abs/1609.05284)

## Datasets

* CNN, DailyMail Dataset

* SQuAD

* Graph Reachability Dataset
    * 2 synthetic datasets to test if the network can answer questions like "Is node_1 connected to node_12"?

## Architecture

* **Memory (M)** - Comprises of the vector representation of the document and the question (encoded using GRU or other RNNs).

* **Attention** - Attention vector (**x<sub>t</sub>**) is a function of current internal state **s<sub>t</sub>** and external memory **M**. The state and memory are passed through FCs and fed to a similarity function.

* **Internal State (s<sub>t</sub>)** - Vector representation of the question state computed by a RNN using the previous internal state and the attention vector **x<sub>t</sub>**

* **Termination Gate (T<sub>t</sub>)** - Uses a logistic regression model to generate a random binary variable using the current internal state **s<sub>t</sub>**.

* **Answer** - Answer module is triggered when **T<sub>t</sub> = 1**.
    * For CNN and DailyMail, a linear projection of GRU outputs is used to predict the answer from candidate entities.
    * For SQuAD, the position of the first and the last word from the answer span are predicted.
    * For Graph Reachability, a logistic regression module is used to predict yes/no as the answer.

* **Reinforcement Learning** - For the RL setting, reward at time **t**, **r<sub>t</sub>** = 1 if **T<sub>t</sub>** = 1 and answer is correct. Otherwise **r<sub>t</sub> = 0**

* **Workflow** - Given a passage p, query q and answer a:

    * Extract memory using p

    * Extract initial hidden state using q

    * ReasoNet executes all possible episodes that can be enumerated by setting an upper limit on the number of passes.

    * These episodes generate actions and answers that are used to train the ReasoNet.

* **Result**
    
    * CNN, DailyMail Corpus

        * ReasoNet outperforms all the baselines which use fixed number of reasoning steps and could benefit by capturing the word alignment signals between query and passage.

    * SQuAD

        * At the time of submission, ReasoNet was ranked 2nd on the [SQuAD leaderboard](https://rajpurkar.github.io/SQuAD-explorer/) and as of 9th July 2017, it is ranked 4th.

    * Graph Reachability Dataset

        * ReasoNet - Standard ReasoNet as described above.

        * ReasoNet-Last - Use the prediction from the **T<sub>max</sub>**

        * ReasoNet > ReasoNet-Last > Deep LSTM Reader

        * ReasoNet converges faster than ReasoNet-Last indicating that the terminate gate is useful.

* **Notes**
    
    * As such there is nothing discouraging the ReasoNet to make unnecessary passes over the passage.
    * In fact, the modal value of the number of passes = upper bound on the number of passes.
    * This effect is more prominent for large graph indicating that the ReasoNet may try to play safe by performing extra passes.
    * It would be interesting to see if the network can be discouraged from making unnecessary passed by awarding a small negative reward for each pass.