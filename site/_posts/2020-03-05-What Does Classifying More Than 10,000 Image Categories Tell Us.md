---
layout: post
title: What Does Classifying More Than 10,000 Image Categories Tell Us?
comments: True
excerpt: 
tags: ['2010', 'ECCV 2010', AI, CV, ECCV, ImageNet]

---

* The paper is among the first to study image classification at a large scale (10000 classes and 9 million examples).

* This is a relatively old paper (2010). Some of the findings may not be relevant anymore. For instance, specific scaling challenges have been significantly overcome. Moreover, the paper uses approaches like SVM and KNN (popular at that time) and not use CNNs.

* Other observations of the paper are still very relevant, and it is an educating paper. For example, since ImagetNet classes are based on WordNet, the paper looks at the effect of semantic relations (tree) of categories on the performance of the training models.

* [Link to the paper](http://openaccess.thecvf.com/content_cvpr_2015/papers/Jain_What_do_15000_2015_CVPR_paper.pdf)

* The paper considers three variants of the ImageNet dataset - ImageNet 10K (10184 classes), ImageNet 7K (7404 classes) and ImageNet 1K (1000 classes).

* They also consider smaller variants with randomly sampled classes or cases where the examples are sampled from one high-level category like vehicles.

* SVM and KNN models are used with features like Bag of Words, GIST descriptors, and spatial pyramid of histograms.

* Observations
    
    * A model that performs well on the smaller dataset (with fewer classes) may not perform well on the larger dataset (with more classes).

    * There seems to be an approximate correlation between the structure of the semantic hierarchy of the labels (obtained via WordNet) and visual confusion between the categories.

    * For example, consider two high-level concepts - says artifacts and animals. The model is less likely to confuse between the classes across the high-level concepts but more likely to confuse between the classes in the respective concepts.

    * For dense categories (categories where the classes are semantically more closely related to each other), the model tends to make more mistakes (even if the number of classes is fewer).

    * Accounting for the label hierarchy (in the loss function) improves the classification performance.