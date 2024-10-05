---
title: 'Variable Selection Methods'
date: 2024-10-05
permalink: /posts/2024/10/variable-selection /
tags:
  - machine learning
  - variable selection
  - regression
---
This post is about variable selection methods for regression problems. 

Minimal Redundancy Maximum Relevance (MRMR)
========
In feature selection, Mutual Information plays a key role in understanding the dependence between variables. Mutual Information, denoted as $I(x, z)$, measures the mutual dependence between two variables $X$ and $Z$. The formula is:

$$
I(x,z) = \sum_{x\in X, z\in Z} P(x,z)\log\left( \frac{P(x,z)}{P(x)P(z)} \right)
$$

When $X$ and $Z$ are independent, $I$ equals zero. This serves as the foundation for MRMR, which aims to find an optimal set of features by maximizing relevance to the response variable while minimizing redundancy between features.

**MRMR Algorithm**:
1. Select the feature with the largest relevance and add it to an empty set, $S$.
2. Identify features with nonzero relevance and zero redundancy, and add them to a new set, $T$.
3. If no such feature exists in $T$, move to Step 4. Otherwise, add the feature with the largest relevance to $S$.
4. Repeat Step 2 until the redundancy for all features in $T$ is nonzero.
5. Select the feature in $T$ that has the largest **Mutual Information Quotient (MIQ)** and nonzero redundancy. Add this feature to $S$.
6. Continue until all features in $T$ have zero relevance.
The Mutual Information Quotient (MIQ) is defined as:

$$
MIQ_x = \max_{x \in T} \left(\frac{I(x,y)}{\frac{1}{|S|} \sum_{z \in S} I(x,z)} \right) 
$$

Once relevance is zero for all features in $T$, any remaining features with zero relevance are added to $S$ in random order.

Variable Importance Projection (VIP) Score using Partial Least Squares (PLS)
======

**Partial Least Squares (PLS)** regression is a method used to model a response variable when there are many predictor variables, which are highly correlated or even collinear. PLS constructs new predictor variables, known as components, as linear combinations of the original predictors. It takes the response variable into account, often leading to models that fit the response variable with fewer components. 

The **Variable Importance in Projection (VIP)** method summarizes the influence of individual variables on the PLS model. The VIP score for variable j is defined as follows:

$$
VIP_j = \sqrt{\frac{p\sum_{f=1}^{F}w_{jf}^2SSY_f}{SSY_{total}}}
$$

Where:
- $w_{jf}$ is the weight value for the $j$-th predictor variable and $f$-th component,
- $SSY_f$ is the sum of squares of the explained variance for the $f$-th component,
- $p$ is the number of predictor variables,
- $SSY_{total}$ is the total sum of squares of the explained variance of the response variable.

The weight $w_{jf}$ indicates the importance of the $j$-th variable in each $f$-th component. The **VIP score ($VIP_j$)** measures the global contribution of the $j$-th variable in the complete PLS model. 

VIP scores are useful for identifying the variables that contribute most to the explanation of variance in the response variable. Since the average of the squared VIP scores equals 1, variables with a VIP score greater than 1 are considered important for the projection of the PLS model.


References
=======
**MRMR**:

The original paper: [Minimum redundancy feature selection from microarray gene expression data](https://pubmed.ncbi.nlm.nih.gov/15852500/)

MATLAB help documentation: [Rank features for regression using minimum redundancy maximum relevance (MRMR) algorithm](https://www.mathworks.com/help/stats/fscmrmr.html)

**VIP Score Using PLS**:

[Comparison of the variable importance in projection (VIP) and of the selectivity ratio (SR) methods for variable selection and interpretation](https://analyticalsciencejournals.onlinelibrary.wiley.com/doi/full/10.1002/cem.2736)
