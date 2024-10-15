---
title: Successive Projections Algorithm (SPA) for variable selection
date: 2024-10-14
permalink: /posts/2024/10/variable-selection-spa /
categories:
  - Data Analysis
tags:
  - Variable Selection
  - Feature Selection
  - Data Analysis
  - Machine Learning
  - CARS Algorithm
  - Partial Least Squares (PLS)
  - High-Dimensional Data
  - Spectral Analysis
---
This post is about Successive Projections Algorithm (SPA) for variable selection.

### Introduction
The **Successive Projections Algorithm (SPA)** is a technique for variable selection, especially when you are looking to identify variables (or features) that are linearly independent. In this blog, we explore how to leverage QR decomposition to implement SPA in a more efficient and computationally friendly way. While QR decomposition is not the traditional method used for SPA, it provides an excellent alternative, especially when handling large datasets where computational efficiency is critical.

### Successive Projections Algorithm
The goal of SPA is to select a subset of variables such that these variables are as linearly independent as possible. This is useful when you have many potentially redundant variables, and you want to select only those that provide the most new information
The classical SPA works by iteratively selecting variables:
1. Start with an initial variable.
2. Project all other variables onto the subspace orthogonal to the selected ones.
3. Select the next variable that is farthest from this subspace (i.e., the most independent).
4. Repeat this process until the desired number of variables is selected.

### QR Decomposition for SPA
**QR decomposition** is a matrix factorization technique that splits a matrix into two components:
- `Q` (an orthogonal matrix) and 
- `R` (an upper triangular matrix).
When combined with **column pivoting**, QR decomposition provides a way to reorder the columns of a matrix based on their linear independence. This column reordering naturally aligns with the goal of SPA—selecting the most independent variables. 

Using QR decomposition in place of iterative projections can offer several advantages:
- **Efficiency**: QR decomposition is computationally optimized and can handle large datasets with ease.
- **Simplicity**: The column pivoting mechanism directly provides an ordering of variables, which can serve as the selection criteria in SPA.
- **Flexibility**: While QR decomposition does not exactly mimic the step-by-step projections of SPA, it offers a reasonable approximation with faster execution.

### Implementing SPA Using QR Decomposition

Below is a step-by-step guide to implementing SPA using the built-in QR decomposition function in MATLAB.

```matlab
function chain = projections_qr(X, k, M)
    % Projections routine for the Successive Projections Algorithm using the
    % built-in QR function of Matlab
    %
    % Inputs:
    % X --> Matrix of predictor variables (# objects N x # variables K)
    % k --> Index of the initial column for the projection operations
    % M --> Number of variables to include in the chain
    %
    % Outputs:
    % chain --> Index set of the variables selected through SPA using QR

    % Make a copy of X to be modified
    X_projected = X;

    % Compute the norms of each column
    norms = sum(X_projected.^2);
    norm_max = max(norms); % Find the largest norm

    % Scale the kth column to ensure it has the largest norm
    X_projected(:, k) = X_projected(:, k) * 2 * norm_max / norms(k);

    % Perform QR decomposition with column pivoting
    [~, ~, order] = qr(X_projected, 0);

    % Select the first M variables based on the column ordering from QR
    chain = order(1:M)';
end
```
### Explanation of the Code
#### Input Parameters:
- **`X`**: The matrix of predictor variables, where rows represent samples and columns represent features.
- **`k`**: The index of the initial column that will be selected first.
- **`M`**: The number of variables to select.

#### Column Norm Calculation:
The algorithm calculates the norms of each column and finds the largest norm. This step ensures that the algorithm has a baseline to compare the magnitude of columns.

#### Scaling the Initial Column:
The initial column (indexed by `k`) is scaled to have the largest norm, ensuring that it is selected first during the QR decomposition process. This scaling step is unique to this implementation and ensures that the first column has priority in the selection process.

#### QR Decomposition with Pivoting:
The built-in `qr` function in MATLAB performs the decomposition with column pivoting. The third output, `order`, contains the indices of the columns in the order of their importance (how well they explain variance while being independent from others).

#### Selecting the Top M Variables:
The variable selection is done by picking the first `M` columns based on the `order` vector returned by the QR decomposition.

### Why QR-Based SPA Works
While this method doesn't explicitly project one variable at a time (as done in traditional SPA), QR decomposition with column pivoting indirectly achieves the same goal. Here’s why it works:

- **QR decomposition** identifies columns that are most independent from one another, which aligns with SPA's goal of selecting variables that are linearly independent.
- The **pivoting mechanism** in QR reorders the variables, giving you an immediate ranking of the most informative (independent) variables.
- The first column is scaled to ensure it is prioritized in the selection, mimicking the "starting variable" behavior of traditional SPA.

### Comparison: Classical SPA vs. QR-based SPA

| **Aspect**                     | **Classical SPA**                                      | **QR-based SPA**                                 |
|---------------------------------|-------------------------------------------------------|--------------------------------------------------|
| **Projection Method**           | Iteratively projects variables onto orthogonal subspaces | Uses QR decomposition to reorder variables       |
| **Variable Selection**          | Selects variables one at a time                       | Selects variables in one step based on column pivoting |
| **Computational Efficiency**    | Less efficient for large datasets due to iterative projections | More efficient for large datasets due to QR optimization |
| **Starting Variable**           | Begins with a user-selected variable                  | Begins with a scaled version of the user-selected variable |
| **Main Goal**                   | Maximize linear independence between variables        | Maximize linear independence between variables   |

### When to Use QR-based SPA?

- **Large datasets**: When working with large datasets, QR-based SPA can offer significant performance benefits due to its computational efficiency.
- **Fast variable selection**: If you need a fast method to select a subset of variables that are maximally independent, QR-based SPA provides a quick solution.
- **Simpler implementation**: QR decomposition is a widely-used linear algebra technique, and it simplifies the variable selection process by avoiding iterative projections.

### Conclusion
The **QR decomposition method** provides a convenient and efficient alternative to traditional SPA for variable selection. While it differs from the classical algorithm in the way projections are handled, it aligns with the underlying goal of SPA: selecting linearly independent variables that are maximally informative.

For practical applications, particularly with large datasets, the QR-based approach offers a computationally attractive solution without sacrificing the quality of variable selection. By leveraging MATLAB’s built-in QR decomposition function with column pivoting, this method ensures efficient and robust feature selection.

Give it a try in your own projects and see how it can streamline the feature selection process!

### Further Reading:
- For more details on QR decomposition, check out [MATLAB’s QR documentation](https://www.mathworks.com/help/matlab/ref/qr.html).
- Learn more about the classical **Successive Projections Algorithm (SPA)** in the context of spectral analysis [here](https://www.sciencedirect.com/science/article/abs/pii/S0169743901001198).


