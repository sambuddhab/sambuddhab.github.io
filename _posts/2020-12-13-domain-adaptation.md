---
layout: post
title: "Domain adaptation"
date: 2020-12-13
---

This curates concepts and ideas about domain adaptation.

## Domain Adaptation versus Transfer Learning
1. In transfer learning, the source and target feature distributions are similar (same domain). The tasks are different (output).
2. In domain adaptation, the source and target feature distributions are different (different domain). The tasks are same (output).

## Different methods of domain adaptation
Divergence based domain adaptation
- MMD - maximum mean discrepancy
- CORAL / Deep CORAL - correlation discrepancy
- CCD - Contrastive Domain Discrepancy — bring MMD for same labels close, and different labels farther
- Wasserstein metric

Adversarial based domain adaptation
- CoGAN
- Source / target converter
- DANN — appears to work well

Reconstruction based domain adaptation
- Deep reconstruction classification network
- Cycle GAN
- Conditional GAN


## DANN
- Motivation: If the features are modified such that a classifier can not distinguish if an item came from the source distribution or target distribution, then a classifier for positive/negative classes built on the source distribution adapts well on the target distribution
- Underlying theory: 
    - Ben-David’s empirical H divergence. This is approximated by a proxy distance named PAD distance which measures the test error on classifying whether the sample belonged to the source or target distribution
    - Risk defined for target population is low if representation is such that you can get low-source-risk and low-H-divergence  [Theorem ]
- Method
    - Add a regularization term to the NN. This regularization term reduces the empirical H divergence [ ie, ability to tell the origin of a sample ]
    - The regularization term is a maximizer - ie, works as adversarially. Hence called DANN
    - Min-max formulation
- Results:
    - Toy binary classification problem: A half-moon like pattern for positive/negative class in 2D space. The target distribution is just rotated by 35-degree. Showed that DANN works better than NN for classification. PCA done on the hidden layer after NN and DANN respectively. Similar labels of source and target stay together for DANN, but not so for NN. Domain classification fails for DANN - which is good because DANN representation tries to prevent the distinguishability. 
    - On amazon sentiment analysis dataset, DANN is compared to NN and SVM. With and without the mSDA (marginalized Stacked Denoising Autoencoder). It shows less error across most of the domain adaptation tasks  [ 9/12 cases such as book->dvd, book->kitchen, book->electronics etc.]

## Divergence based domain adaptation
### Deep Coral : Correlation alignment for domain adaptation  https://arxiv.org/abs/1607.01719
The problem: Source distribution and target distributions are different. Training on source can result in poor performance on target.
Key idea: Modify the loss function. In addition to minimizing label-prediction (cross-entropy etc), add a term for the minimizing the distance between the feature covariances of source and target distributions (CORAL loss). This prevents ‘overfitting’ on the source distribution.

How done:  Distance between covariance matrices is computed using Frobenius norm. The CORAL loss is applied to the last FC layer in the paper. The source data is used for training to minimize label-prediction loss. Both source and target data used to train for minimizing the CORAL loss.

What not clear:  Each weight update should happen with the combined loss. Are the source and target data sent separately, say in separate batches? Or are these sent together, and based on some ‘tag’ on the input, the custom loss function appropriately picks and constructs the two covariance matrices? Will need to go deeper into tensorFlow apis.

Critique: 


What happens in CORAL?
- Whitening and Coloring
- Min Frobenius norm of source and target covariance matrices

GAN - discriminator and generator play against each other. Objective is to ‘generate’ new samples consistent with a distribution. The discriminator tells which item belongs to a distribution, and which one does not (fake). Training stops when the discriminator fails to discriminate a generated item from a real one.
