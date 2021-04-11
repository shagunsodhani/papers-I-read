---
layout: post
title: HyperNetworks
comments: True
excerpt: 
tags: ['2016', 'ICLR 2017', AI, HyperNetwork, ICLR]

---

## Introduction

* The paper explores HyperNetworks. The idea is to use one network (HyperNetwork) to generate the weights for another network.

* [Link to the paper](https://arxiv.org/abs/1609.09106)

* [Author's implementation](https://github.com/hardmaru/supercell/blob/master/supercell.py)


## Approach

### Static HyperNetworks - HyperNetworks for CNNs

* Consider a $D$ layer CNN where the parameters for the $j^{th}$ layer are stored in a matrix $K^j$ of the shape $N_{in}f_{size} \times N_{out}f_{size}$.

* The HyperNetwork is implemented as a two-layer linear network where the input is a layer embedding $z^j$, and the output is $K^j$.

* The first layer (of the HyperNetwork) maps the input to $N_{in}$ different outputs using $N_{in}$ weight matrices.

* The second layer maps the different $N_{in}$ inputs to $K_{i}$ using a shared matrix. The resulting $N_{in}$ (number of) $K_{i}$ matrices are concatenated to obtain $K^j$.

* As a side note, HyperNetworks have much fewer params than the network for which it produces weights.

* In a general case, the kernel dimensions (across layers) are not of the same size but integer multiples of some basic sizes. In that case, the HyperNetwork can generate kernels for the basic size, which can be concatenated to form larger kernels. This would require additional input embeddings but not require a change in the architecture of HyperNetwork.

### Dynamic HyperNetworks - HyperNetworks for RNNs

* HyperRNNs/HyperLSTMs denote HyperNetworks that generates weights for RNNs/LSTMs.

* HyperRNNs implement a form of relaxed weight sharing - an alternative to the full weight sharing of the traditional RNNs.

* At any timestamp $t$, the input to the HyperRNN is the concatenated vector $x_{t}$ (input to the RNN at time $t$) and the hidden state $h_{t-1}$ of the RNN. The output is the weight for the main RNN at timestep $t$.

* In practice, a *weight scaling vector* $d$ is used to reduce the memory footprint, which would otherwise be $dim$ times the memory of a standard RNN. $dim$ is the dimensionality of the embedding vector $z_j$.

## Experiments

* HyperNetworks are used to train standard CNNs for MNIST and ResNets for CIFAR 10. In these experiments, HyperNetworks slightly underperform the best performing models but uses much fewer parameters.

* HyperLSTMs trained on the Penn Treebank dataset and Hutter Prize Wikipedia dataset outperform the stacked LSTMs and perform similar to layer-norm LSTMs. Interestingly, using HyperLSTMs with layer-norm improves performance over HyperLSTMs.

* Given the similar performance of HyperLSTMs and layer-norm LSTMs, the paper conducted an ablation study to understand if HyperLSTMs learned a weight adjustment policy similar to the statistics-based approach used by layer-norm LSTMs.
    
    * However, the analysis of the histogram of the hidden states suggests that using layer-norm reduces the saturation effect while in HyperLSTMs, the cell is saturated most of the time. This indicates that the two models are learning different policies.

* HyperLSTMs are also evaluated for handwriting sequence generation by training in the IAM online handwriting dataset.
    
    * While HyperLSTMs are quite effective on this task, combining them with layer-norm degrades the performance.

* On the WMT'14 En-to-Fr machine translation task, HyperLSTMs outperform LSTM based approaches.