---
layout: post
title: Net2Net-Accelerating Learning via Knowledge Transfer
comments: True
excerpt: The paper presents a simple yet effective approach for transferring knowledge from a trained neural network to a large, untrained network
tags: ['2016', 'ICLR 2016', 'Knowledge Transfer', 'Lifelong Learning', AI, CV]
---

## Notes

* The paper presents a simple yet effective approach for transferring knowledge from a trained neural network (referred to as the teacher network) to a large, untrained neural network (referred to as the student network).

* The key idea is to use a function-preserving transformation that guarantees that for any given input, the output from the teacher network and the newly created student network would be the same.

* [Link to the paper](https://arxiv.org/abs/1511.05641)

* [Link to an implementation](https://github.com/paengs/Net2Net)

* The approach works as follows - Let us say that the teacher network was represented by the transformation *y = f(x, &theta;)* where *&theta;* refer to the parameters of the network. The task is to choose a new set of parameters *&theta;'* for the student network *g(x, &theta;')* such that for all *x, f(x, &theta;) = g(x, &theta;')*

* To start, we can assume that *f* and *g* are composed of standard linear layers. Layer *i* and *i+1* are represented by weights *W<sub>mxn</sub><sup>i</sup>* and *W<sub>nxp</sub><sup>i+1</sup>*

* We want to grow layer *i* to have *q* output units (where *q* > *n*) and layer *i+1* to have *q* input units. The new weight matrix would be *U<sub>mxq</sub><sup>i</sup>* and *U<sub>qxp</sub><sup>i+1</sup>*

* The first *q* columns (rows) of *W<sup>i</sup>* (*W<sup>i+1</sup>*) would be copied as it is into *U<sup>i</sup>*(*U<sup>i+1</sup>*).

* For filling the remaining *n-q* slots, columns (rows) would be sampled randomly from *W<sup>i</sup>* (*W<sup>i+1</sup>*).

* Finally, each layer in *U<sup>i</sup>* is scaled by dividing by the corresponding replication factor to ensure that the output value of function remains unchanged by the operation.

* Since convolutions can be seen as multiplication by a double block circulant matrix, the approach can be readily extended for convolutional networks.

* The benefits of using this approach are the following:

    * The newly created student network performs at least as good as the teacher network.
    * Any changes to the network are guaranteed to be an improvement.
    * It is safe to optimize all the parameters in the network.

* The variant discussed above is called the **Net2WiderNet** variant. There is another variant called**Net2DeeperNet** that enables the network to grow in depth.

* In that case, a new matrix, *U*, initialized as the identity matrix, is added to the network. Note that unlike the **Net2WiderNet**, this approach would not work with arbitrary activation function between the layers.

## Strengths

* The model can accelerate the training of neural networks, especially during development cycle when the designers try out different models.

* The approach could potentially be used in life-long learning systems where the model is trained over a stream of data and needs to grow over time.

## Limitations

* The function preserving transformations need to be worked out manually. Extra care needs to be taken when operations like concatenation or batch norm are present.
