---
layout: post
title: GNN Explainer - A Tool for Post-hoc Explanation of Graph Neural Networks
comments: True
excerpt: 
tags: ['2019', 'Graph Neural Network', 'Graph Representation', AI, Embedding, GNN, Graph]
---

## Introduction

* Graph Neural Network (GNN) is a family of powerful machine learning (ML) models for graphs that can combine node information with the structural information. 

* One downside of GNNs is that their predictions are hard to interpret.

* The paper proposes GNN Explainer model for solving the problem of interpretability.

* [Paper](https://arxiv.org/abs/1903.03894)

## Desiderata for GNN explanations

* **Local edge fidelity** - identify the subgraph structure (ideally the smallest) that significantly affected the predictions of the GNN. ie identify the important edges in the graph (for a given prediction).

* **Local node fidelity** - identify the import node features and correlations in the features of the neighboring nodes.

* **Single instance and multi-instance explanations** - Support both single instance prediction tasks and multi-instance prediction tasks.

* **Model Agnostic** - Support a large family of models (ideally all)

* **Task Agnostic** - Support a large family of tasks (ideally all)

## Approach

* I first describe the single instance prediction case and use that as the base to describe the multiple instance prediction cases. All the discussion in this section assumes a single instance prediction task.

* **Input**: Trained GNN, a single instance whose prediction is to be explained.

* **Task**: Identify the small subgraph and the small subset of features that explain the prediction.

* **Idea**: Maximize the mutual information (MI) between the GNN and the explanation by learning a *graph mask* which can be used for selecting the relevant subgraph (from the GNN's computational graph) and features (from all layers of the GNN).

* Computational graph of GNN (corresponding to a node) refers to the approx L-hop neighborhood of the node in the graph ie the subgraph formed by nodes and edges whose representation affected the representation of the given node.

### Single-Instance Explanations

* For a node *v*, the information used to predict its label *y* is completely described by its computation graph *G<sub>c</sub>(v)* and the associated feature set *X<sub>c</sub>(v)*. The feature set includes the features of all the nodes in the computation graph.

* When constructing the explaination, only *G<sub>c</sub>(v)* and *X<sub>c</sub>(v)* are used.

* The task can be reformulated as identifying a subgraph *G<sub>S</sub>* (subset of *G<sub>c</sub>(v)*) with associated features *X<sub>S</sub>* which are important when predicting the label *y* for node *v*.

* "Importance" is measured in terms of MI

*MI(Y, (G<sub>S</sub>, X<sub>S</sub>)) = H(Y) - H(Y \| G = G<sub>S</sub>, X = X<sub>S</sub>)* where *H* is the entropy and *Y* is a random variable representing the prediction.

* A further constraint, *\| G<sub>S</sub>\| < k* is imposed to obtain consise explaintations.

* Since *H(Y)* is fixed (recall that the network has already been trained and is now being used in the inference mode), maximizing MI is equivalent to minimizing the conditional entropy *H(Y \| G = G<sub>S</sub>, X = X<sub>S</sub>)*

* This is equivalent to selecting the subgraph that minimizes the uncertainty in the prediction of *y* when the computational graph is *G<sub>c</sub>(v)*

#### Optimiation Process

* Given the exponentially large number of possible subgraphs, we can not directly optimize the given equation.

* A "relaxed"-adjacency matrix (whose values are real numbers in the range 0 to 1) is introduced where each element of this fractional adjacency matrix is smaller than the corresponding element of the original adjacency matrix. Gradient descent can be performed on this adjacency matrix.

* The "relaxed" *G<sub>S</sub>* can be interpreted as a variational approximation of the subgraph distributions of *G<sub>c</sub>(v)* and the objective can be written as *min E<sub>G<sub>S</sub></sub>H(Y \| G = G<sub>S</sub>, X = X<sub>S</sub>)*

* Now the paper makes a big approximation that the GNN is convex so as to leverage the Jensen inequality and push the expectation inside the entropy term to get an upper bound and then minimize that ie *min H(Y \| G = E<sub>s</sub>[G<sub>S</sub>], X = X<sub>S</sub>)*

* The paper reports that the convexity approximation (along with discreteness constraint) works in practice.

* Next, mean field approximation is used to decompose *P(G<sub>S</sub>)* as a multivariate Bernoulli distrbitution ie product of *A<sub>S</sub>(i, j)* for all *(i, j)* belonging to *G<sub>c</sub>(v)*. *A<sub>S</sub>* can be optimized directly and its values represent the expectation of the Bernoulli distrbitution on wether the edge *e<sub>i, j</sub>* exists.

* Given the constraints on *A<sub>S</sub>*, it is easier to learn a mask matrix *M* and optimize that such that *A<sub>S</sub>* = M \* A<sub>c</sub>* Additionally, the sigmod operator can be applied on *M*.

* Once *M* is learned, only the top *k* values are retained.

#### Including Node Features in the Explanation

* Similar to the previous approach, another feature mask is learned (either one for entire GNN or one per node of the GNN) and is used as a feature selector.

* The mask could either be learned such that same set of node features (in terms of dimensions) are selected or a different set of features are selected per node. The paper uses the former as it is more straightforward.

* Just like before, a "relaxed" mask *M<sub>T</sub>* is trained to select features as *M<sub>T</sub> * X<sub>S</sub>*.

* One tricky case is where one feature is important but its value is set to 0. In the case, the value will be masked even though it should not be

* The workaround is to use Monte Carlo (MC) estimates of marginals of the missing features. This gives a way to assign importance scores to each feature dimension and a form of reparameterization trick is used to perform end-to-end learning.

* Masks are encouraged to be discrete by regularizing their element-wise entropy.

* Resulting computation graph is valid as in it allows message passing towards the central node *v*.

## Multi-Instance Explanations

* Given a set of nodes (having the label say *y*),  the task is to obtain a global explanation of the predictions.

* For the given class, a prototypical reference node is chosen by computing the mean of embeddings of all the nodes in the class and then selecting the node which is closest to the mean.

* Now, compute the important computational graph corresponding to this node and align the computational subgraphs of all the other nodes (in the given class) to reference.

* Let *A\** be the adjacency matrix and *X\** be the feature matrix for the explanation corresponding to the reference node. Let *A<sub>v</sub>* and *X<sub>v</sub>* be the adjacency matrix and feature matrix of the to-ber-aligned computational graph.

* A relaed alignment matrix *P* is optimized to align the nodes and features in the two graphs ie we minimize *\|P<sup>T</sup>A<sub>v</sub>P - A\*\| + *\|P<sup>T</sup>X<sub>v</sub>P - X\*\|*

* Choosing concise explanations helps in efficient graph matching.

* For GNNs that compute attention over the entire graph, edges with low attention weight can be pruned to increase efficiency.

## Experiments

* Datasets
    
    * Node classification: BA-Shapes, BA-Community, Tree-Cycles, Tree-Grid

    * Graph classification: MUTAG, Reddit-Binary

* Baselines

    * GRAD - Compute the gradient of the model loss with respect to the adjacency matrix and the node features to be classified and fix the edges with the highest absolute gradient.

    * GAT - Graph Attention Network

* The proposed model seems to outperform the baselines both qualitatively and quantitatively. But the results should be taken with a grain of salt as only 2 baselines are considered.